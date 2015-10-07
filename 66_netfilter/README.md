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

