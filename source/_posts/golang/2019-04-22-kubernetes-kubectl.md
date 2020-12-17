---
layout: post
title:  "Kubectl!"
date:   2019-04-22 10:22:16 +0800
tags: ["kubernetes"]
published: true
comments: true
script: [post.js]
excerpted: |
    kubernetes kubectl ...
day_quote:
  title: The word of the day!
  description: |
    Put a very powerful message.
---


# 概述
kubernetes中的命令行接口叫做kubectl，用来控制kubernetes集群。

kubectl采用Cobra库进行构建，关于Cobra的简单使用，请参考[Cobra Cli](https://guodongq.github.io/2019/04/19/golang-cli-about-cobra/)


# 源码结构
- api: 接口文档，基本上都是json源码
- build: 构建脚本
- cluster: 关于集群的相关配置
- cmd: 所有二进制可执行文件入口代码，即各种命令的接口代码
- docs: 文档
- Godeps: 项目依赖的Go第三方包，比如docker客户端sdk、rest等
- hack: 工具箱，各种编译、构建、校验脚本
- logo: kubernetes logo
- pkg: 项目diamante主目录，cmd只是接口，这里是具体实现，cmd类似业务代码，pkg类似核心
- plugin: 插件
- staging: 外部存储库
- test: 测试相关工具
- third_party: 第三方工具
- translations:  翻译说明文档
- vendor: 代码库依赖

# kubectl结构
## 1.main()
- Location: `cmd/kubectl/kubectl.go`

```
func main() {
         // 使用时间作为随机数生成器，具体参考https://guodongq.github.io/2018/12/02/golang-time-seed
	rand.Seed(time.Now().UnixNano())

        // 创建kubectl命令以及子命令，最后生成*cobra.Command对象
	command := cmd.NewDefaultKubectlCommand()    

	// TODO: once we switch everything over to Cobra commands, we can go back to calling
	// cliflag.InitFlags() (by removing its pflag.Parse() call). For now, we have to set the
	// normalize func and add the go flag set by hand.
	pflag.CommandLine.SetNormalizeFunc(cliflag.WordSepNormalizeFunc)
	pflag.CommandLine.AddGoFlagSet(goflag.CommandLine)
	// cliflag.InitFlags()
	logs.InitLogs()
	defer logs.FlushLogs()

       // 执行命令，Cobra主框架函数
	if err := command.Execute(); err != nil {
		fmt.Fprintf(os.Stderr, "%v\n", err)
		os.Exit(1)
	}
}
```


## 2.cmd.NewDefaultKubectlCommand()
- Location: `pkg/kubectl/cmd/cmd.go`

```
func NewDefaultKubectlCommand() *cobra.Command {
    return NewDefaultKubectlCommandWithArgs(
        NewDefaultPluginHandler(plugin.ValidPluginFilenamePrefixes), 
        os.Args, 
        os.Stdin,
        os.Stdout, 
        os.Stderr)
}
```

创建kubectl命令使用默认的参数：
- plugin.ValidPluginFilenamePrefixes: 定义了一个切片，里面包含一个元素`[]string{"kubectl"}`
- os.Args: 命令行参数,包含程序路径本身。类型是 []string ，也就是字符串切片
- os.Stdin/os.Stdout/os.Stderr: 每次在调用os 包的时候都会创建三个全局的变量，本质上是创建三个绑定在std输出/输入IO的File对象, 所以我们像对待普通的File对象一样对待这三个对象即可
   ```
       var (
    	Stdin  = NewFile(uintptr(syscall.Stdin), "/dev/stdin")
    	Stdout = NewFile(uintptr(syscall.Stdout), "/dev/stdout")
    	Stderr = NewFile(uintptr(syscall.Stderr), "/dev/stderr")
   	)
   ```
-  NewDefaultPluginHandler: 实例化类DefaultPluginHandler，该结构是接口PluginHandler的实现类，主要实现了两个方法
   ```
      type PluginHandler interface {
	    Lookup(filename string) (string, bool)
	    Execute(executablePath string, cmdArgs, environment []string) error
      }
   ```


## 3.NewDefaultKubectlCommandWithArgs()
- Location: `pkg/kubectl/cmd/cmd.go`

```
func NewDefaultKubectlCommandWithArgs(pluginHandler PluginHandler, args []string, in io.Reader, out, errout io.Writer) *cobra.Command {
        // 构造kubectl命令
	cmd := NewKubectlCommand(in, out, errout)

	if pluginHandler == nil {
		return cmd
	}
	if len(args) > 1 {
                // cmdPathPieces是除了文件名之外的其他参数
		cmdPathPieces := args[1:]

		// only look for suitable extension executables if
		// the specified command does not already exist
		if _, _, err := cmd.Find(cmdPathPieces); err != nil {
			if err := HandlePluginCommand(pluginHandler, cmdPathPieces); err != nil {
				fmt.Fprintf(errout, "%v\n", err)
				os.Exit(1)
			}
		}
	}
	return cmd
}
```

## 4.NewKubectlCommand()
- Location： `pkg/kubectl/cmd/cmd.go`

```
func NewKubectlCommand(in io.Reader, out, err io.Writer) *cobra.Command {
	// 利用cobra库初始化构造一个*cobra.Command对象
	cmds := &cobra.Command{
		Use:   "kubectl",
		Short: i18n.T("kubectl controls the Kubernetes cluster manager"),
		Long: templates.LongDesc(`
      kubectl controls the Kubernetes cluster manager.
      Find more information at:
            https://kubernetes.io/docs/reference/kubectl/overview/`),
		Run: runHelp,
		// PresistentPreRunE和PresistentPostRunE两个方法分别在Run方法执行之前或者之后运行
		PersistentPreRunE: func(*cobra.Command, []string) error {
			return initProfiling()
		},
		PersistentPostRunE: func(*cobra.Command, []string) error {
			return flushProfiling()
		},
		// bash 命令自动补全
		BashCompletionFunction: bashCompletionFunc,
	}
	// 初始化*flag.FlagSet对象
	flags := cmds.PersistentFlags()
	
	// 将flags中的 "_" 替换成 "-" 并打印日志
	flags.SetNormalizeFunc(cliflag.WarnWordSepNormalizeFunc) // Warn for "_" flags

	// 将flags中的 "_" 替换成 "-"
	flags.SetNormalizeFunc(cliflag.WordSepNormalizeFunc)

	// 增加两个变量用于配置 "profile"和"profile-output"
	addProfilingFlags(flags)

	// 实例化*ConfigFlags对象
	kubeConfigFlags := genericclioptions.NewConfigFlags(true).WithDeprecatedPasswordFlag()
	
	//将 *ConfigFlags对象中的每一个元素，通过指针的方式绑定到 *pflag.FlagSet对象
	kubeConfigFlags.AddFlags(flags)
	
	// 实例化*MatchVersionFlags对象，该对象内部持有genericclioptions.RESTClientGetter接口，而*ConfigFlags(kubeConfigFlags)对象实现了该接口，并作为入参实例化了*MatchVersionFlags对象, 其实*MatchVersionFlags也是实现了接口genericclioptions.RESTClientGetter接口
	matchVersionKubeConfigFlags := cmdutil.NewMatchVersionFlags(kubeConfigFlags)
	
	// 往*pflag.FlagSet对象中添加了一个bool类型的flag【--match-server-version】,它的值来自上面matchVersionKubeConfigFlags对象中的RequireMatchedServerVersion属性的值
	matchVersionKubeConfigFlags.AddFlags(cmds.PersistentFlags())

	// 将*pflag.FlagSet对象添加到原生的*goflag.FlagSet对象中，最后再将原生的newSet(*goflag.FlagSet)添加到 *pflag.FlagSet对象的属性addedGoFlagSets上
	cmds.PersistentFlags().AddGoFlagSet(flag.CommandLine)

	// 实例化接口Factory(factoryImpl), 将matchVersionKubeConfigFlags（实现了接口genericclioptions.RESTClientGetter）作为参数传递给factoryImpl的属性clientGetter上
	f := cmdutil.NewFactory(matchVersionKubeConfigFlags)

	// 传入nil，会获取系统的语言(依次获取环境变量中的LC_ALL/LC_MESSAGES/LANG），将结果返回，如果是zh_CN.UTF-8将返回zh_CN， 默认会返回default字符串，主要是加载翻译的文件{kubectl/en_US/LC_MESSAGES/k8s.po(.mo)}
	i18n.LoadTranslations("kubectl", nil)

	// 给命令以及子命令设置 "_"变成"-", 包括所有的命令*cobra.Command中的属性flags以及pflags
	cmds.SetGlobalNormalizationFunc(cliflag.WarnWordSepNormalizeFunc)
	
	//将std.in/std.out/std.errout放在类IOStreams中封装
	ioStreams := genericclioptions.IOStreams{In: in, Out: out, ErrOut: err}

	// 定义kubectl的子命令，按照分组的方式，传入了上面的 factoryImpl(f)实例和ioStreams
	groups := templates.CommandGroups{
		{
			Message: "Basic Commands (Beginner):",
			Commands: []*cobra.Command{
				create.NewCmdCreate(f, ioStreams),
				expose.NewCmdExposeService(f, ioStreams),
				run.NewCmdRun(f, ioStreams),
				set.NewCmdSet(f, ioStreams),
			},
		},
		{
			Message: "Basic Commands (Intermediate):",
			Commands: []*cobra.Command{
				explain.NewCmdExplain("kubectl", f, ioStreams),
				get.NewCmdGet("kubectl", f, ioStreams),
				edit.NewCmdEdit(f, ioStreams),
				delete.NewCmdDelete(f, ioStreams),
			},
		},
		{
			Message: "Deploy Commands:",
			Commands: []*cobra.Command{
				rollout.NewCmdRollout(f, ioStreams),
				rollingupdate.NewCmdRollingUpdate(f, ioStreams),
				scale.NewCmdScale(f, ioStreams),
				autoscale.NewCmdAutoscale(f, ioStreams),
			},
		},
		{
			Message: "Cluster Management Commands:",
			Commands: []*cobra.Command{
				certificates.NewCmdCertificate(f, ioStreams),
				clusterinfo.NewCmdClusterInfo(f, ioStreams),
				top.NewCmdTop(f, ioStreams),
				drain.NewCmdCordon(f, ioStreams),
				drain.NewCmdUncordon(f, ioStreams),
				drain.NewCmdDrain(f, ioStreams),
				taint.NewCmdTaint(f, ioStreams),
			},
		},
		{
			Message: "Troubleshooting and Debugging Commands:",
			Commands: []*cobra.Command{
				describe.NewCmdDescribe("kubectl", f, ioStreams),
				logs.NewCmdLogs(f, ioStreams),
				attach.NewCmdAttach(f, ioStreams),
				cmdexec.NewCmdExec(f, ioStreams),
				portforward.NewCmdPortForward(f, ioStreams),
				proxy.NewCmdProxy(f, ioStreams),
				cp.NewCmdCp(f, ioStreams),
				auth.NewCmdAuth(f, ioStreams),
			},
		},
		{
			Message: "Advanced Commands:",
			Commands: []*cobra.Command{
				diff.NewCmdDiff(f, ioStreams),
				apply.NewCmdApply("kubectl", f, ioStreams),
				patch.NewCmdPatch(f, ioStreams),
				replace.NewCmdReplace(f, ioStreams),
				wait.NewCmdWait(f, ioStreams),
				convert.NewCmdConvert(f, ioStreams),
				kustomize.NewCmdKustomize(ioStreams),
			},
		},
		{
			Message: "Settings Commands:",
			Commands: []*cobra.Command{
				label.NewCmdLabel(f, ioStreams),
				annotate.NewCmdAnnotate("kubectl", f, ioStreams),
				completion.NewCmdCompletion(ioStreams.Out, ""),
			},
		},
	}
	//将定义的groups命令组，添加到cmds实例上，即调用了cmds.AddCommand(cmds... *cobra.Command)命令
	groups.Add(cmds)

	filters := []string{"options"}
	
	// 定义alpha命令，当alpha命令没有子命令的时候，将其加入filters列表中
	alpha := NewCmdAlpha(f, ioStreams)
	if !alpha.HasSubCommands() {
		filters = append(filters, alpha.Name())
	}
	
	// 使用go template将入参进行组合，最后全部设置到对象 cmds中
	templates.ActsAsRootCommand(cmds, filters, groups...)

	// 判断命令的flag中是否包含namespace/context/cluster/user这几项，如果包含，就生成各自的annotations map，每个元素中都包含两个值，其中第一个值是cobra_annotation_bash_completion_custom，结果是类似这种"namespace":["cobra_annotation_bash_completion_custom", "_kubectl_get_resource_namespace"]等
	for name, completion := range bashCompletionFlags {
		if cmds.Flag(name) != nil {
			if cmds.Flag(name).Annotations == nil {
				cmds.Flag(name).Annotations = map[string][]string{}
			}
			cmds.Flag(name).Annotations[cobra.BashCompCustom] = append(
				cmds.Flag(name).Annotations[cobra.BashCompCustom],
				completion,
			)
		}
	}
	// 增加alpha命令
	cmds.AddCommand(alpha)
	
	// 增加config命令
	cmds.AddCommand(cmdconfig.NewCmdConfig(f, clientcmd.NewDefaultPathOptions(), ioStreams))
	
	// 增加plugin命令
	cmds.AddCommand(plugin.NewCmdPlugin(f, ioStreams))
	
	// 增加version命令
	cmds.AddCommand(version.NewCmdVersion(f, ioStreams))
	
	// 增加api-verisons命令
	cmds.AddCommand(apiresources.NewCmdAPIVersions(f, ioStreams))
	
	// 增加api-resources命令
	cmds.AddCommand(apiresources.NewCmdAPIResources(f, ioStreams))
	
	// 增加options命令
	cmds.AddCommand(options.NewCmdOptions(ioStreams.Out))

	return cmds
}
```
