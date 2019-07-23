
#安装openswan
yum -y install openswan
#启动
systemclt start ipsec
#检查配置
ipsec verify

#根据失败提示修改内核参数
vim /etc/sysctl.conf
net.ipv4.ip_forward = 1  # 开启转发
net.ipv4.conf.default.rp_filter = 0

# 关闭icmp重定向
sysctl -a | egrep "ipv4.*(accept|send)_redirects" | awk -F "=" '{print$1"= 0"}' >> /etc/sysctl.conf
sysctl -p


#连接配置
vim /etc/ipsec.conf

config setup
#	plutodebug=all
	plutostderrlog=/var/log/pluto.log
	virtual_private=%v4:10.0.0.0/8,%v4:192.168.0.0/16,%v4:172.25.0.0/12

conn dianxin-1-30m
	authby=secret
	type=tunnel
	auto=add  //add 代表手动  start代表自动
	pfs=yes

	left=10.14.240.234  //本机IP
	leftsubnet=10.0.0.0/8

	right=180.169.233.181  //对端IP
	rightsubnet=192.168.0.0/16

	esp=aes256-sha1;modp1024
	keyexchange=ike
	ike=aes256-sha1;modp1024
	salifetime=28800s

#	dpdaction=restart
#	dpddelay=10
#	dpdtimeout=30

conn liantong-1-100m
        authby=secret
        type=tunnel
        auto=add
        pfs=yes

        left=10.14.240.234
        leftsubnet=10.0.0.0/8

        right=140.206.62.210
        rightsubnet=192.168.0.0/16

        esp=aes256-sha1;modp1024
        keyexchange=ike
        ike=aes256-sha1;modp1024
        salifetime=28800s

#       dpdaction=restart
#       #       dpddelay=10
#       #       dpdtimeout=30


conn liantong-2-100m
        authby=secret
        type=tunnel
        auto=add
        pfs=yes

        left=10.14.240.234
        leftsubnet=10.0.0.0/8

        right=140.206.62.214
        rightsubnet=192.168.0.0/16

        esp=aes256-sha1;modp1024
        keyexchange=ike
        ike=aes256-sha1;modp1024
        salifetime=28800s

#       dpdaction=restart
#       #       #       dpddelay=10
#       #       #       dpdtimeout=30


#secrets配置
vim /etc/ipsec.secrets
# 本机IP     对端IP             预共享key
10.14.240.234 180.169.233.181 : PSK "runxsports"
10.14.240.234 140.206.62.210 : PSK "runxsports"
10.14.240.234 140.206.62.214 : PSK "runxsports"



#ipsec auto --up net-to-net      /当配置的auto=add 时，才需要手动启动，配置为start则不需要。