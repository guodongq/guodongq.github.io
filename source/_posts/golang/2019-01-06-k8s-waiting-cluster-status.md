---
layout: post title:  "kubernetes waiting cluster ready!"
date:   2019-01-06 10:22:16 +0800 tags: ["k8s","golang"]
published: true comments: true script: [post.js]
excerpted: | kubernetes waiting cluster ready ... day_quote:
title: The word of the day!
description: | Put a very powerful message.
---

<!--more-->

```
// WaitForClusterAvailable returns true if all endpoints in the cluster are available after retry attempts, an error is returned otherwise
func (c Client) WaitForClusterAvailable(retries int, retryInterval time.Duration) (bool, error) {
	for i := 0; i < retries; i++ {
		if i > 0 {
			fmt.Printf("[util/etcd] Waiting %v until next retry\n", retryInterval)
			time.Sleep(retryInterval)
		}
		fmt.Printf("[util/etcd] Attempting to see if all cluster endpoints are available %d/%d\n", i+1, retries)
		resp, err := c.ClusterAvailable()
		if err != nil {
			switch err {
			case context.DeadlineExceeded:
				fmt.Println("[util/etcd] Attempt timed out")
			default:
				fmt.Printf("[util/etcd] Attempt failed with error: %v\n", err)
			}
			continue
		}
		return resp, nil
	}
	return false, errors.New("timeout waiting for etcd cluster to be available")
}
```
