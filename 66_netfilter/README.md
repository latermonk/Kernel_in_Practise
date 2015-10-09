## netfilter practise



## iptable basic


![](https://github.com/latermonk/Kernel_in_Practise/raw/master/66_netfilter/img/00_waht_is_netfilter.png)    
![](https://github.com/latermonk/Kernel_in_Practise/raw/master/66_netfilter/img/01_point.jpg)    
![](https://github.com/latermonk/Kernel_in_Practise/raw/master/66_netfilter/img/02_point_and_tables.jpg)    

## iptables forbidden ping localhost

if U want 

### iptables way 

#### enable:

	iptables -A INPUT -p icmp --icmp-type 8 -s 0/0 -j DROP 

#### disable:

	iptables -D INPUT -p icmp --icmp-type 8 -s 0/0 -j DROP


### module programming

	module progrmming

####	drop_ping.c
####    Makefile_drop_ping



##  


	/* Responses from hook functions. */
	#define NF_DROP 		0
	#define NF_ACCEPT 		1
	#define NF_STOLEN 	    2
	#define NF_QUEUE 		3
	#define NF_REPEAT 		4
	#define NF_STOP 		5
	#define NF_MAX_VERDICT  NF_STOP






## 12

	NF_HOOK(	uint8_t pf, unsigned int hook, struct sk_buff *skb,
				struct net_device *in, struct net_device *out,
				int (*okfn)(struct sk_buff *))

	{

		return NF_HOOK_THRESH(pf, hook, skb, in, out, okfn, INT_MIN);

	}


## 34

	NF_HOOK_THRESH(uint8_t pf, unsigned int hook, struct sk_buff *skb,
		       struct net_device *in, struct net_device *out,
		       int (*okfn)(struct sk_buff *), int thresh)
	{
		int ret = nf_hook_thresh(pf, hook, skb, in, out, okfn, thresh);
		if (ret == 1)
			ret = okfn(skb);
		return ret;
	}


## 56

	static inline int nf_hook_thresh(u_int8_t pf, unsigned int hook,
					 struct sk_buff *skb,
					 struct net_device *indev,
					 struct net_device *outdev,
					 int (*okfn)(struct sk_buff *), int thresh)
	{
	#ifndef CONFIG_NETFILTER_DEBUG
		if (list_empty(&nf_hooks[pf][hook]))
			return 1;
	#endif
		return nf_hook_slow(pf, hook, skb, indev, outdev, okfn, thresh);
	}


## 78

	/* Returns 1 if okfn() needs to be executed by the caller,
	 * -EPERM for NF_DROP, 0 otherwise. */
	int nf_hook_slow(u_int8_t pf, unsigned int hook, struct sk_buff *skb,
			 struct net_device *indev,
			 struct net_device *outdev,
			 int (*okfn)(struct sk_buff *),
			 int hook_thresh)
	{
		struct list_head *elem;
		unsigned int verdict;
		int ret = 0;
	
		/* We may already have this, but read-locks nest anyway */
		rcu_read_lock();
	
		elem = &nf_hooks[pf][hook];
	next_hook:
		verdict = nf_iterate(&nf_hooks[pf][hook], skb, hook, indev,
				     outdev, &elem, okfn, hook_thresh);
		if (verdict == NF_ACCEPT || verdict == NF_STOP) {
			ret = 1;
		} else if (verdict == NF_DROP) {
			kfree_skb(skb);
			ret = -EPERM;
		} else if ((verdict & NF_VERDICT_MASK) == NF_QUEUE) {
			if (!nf_queue(skb, elem, pf, hook, indev, outdev, okfn,
				      verdict >> NF_VERDICT_BITS))
				goto next_hook;
		}
		rcu_read_unlock();
		return ret;
	}
	EXPORT_SYMBOL(nf_hook_slow);


####

	