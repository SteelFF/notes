### ipv4当中的特殊地址(32位)

**127.0.0.0/8** 本地环回地址，用于本地功能测试。可以用ping进行测试（注意：127.0.0.0和127.255.255.255无法被ping通）

**10.0.0.0/8** 私有地址（所谓的私有地址就是在互联网上不使用，而被用在 局域网络 中的地址）用于局域网络访问

**172.16.0.0/12** 私有地址（所谓的私有地址就是在互联网上不使用，而被用在 局域网络 中的地址）用于局域网络访问

**192.168.0.0/16** 私有地址（所谓的私有地址就是在互联网上不使用，而被用在 局域网络 中的地址）用于局域网络访问

### ipv6当中的特殊地址（128位，采用16进制显示，一共是8个16比特段，一个段4个16进制数字，开头的0省略，总计32个16进制数字）

::1/128 唯一的本地环回地址
::/128 不确定地址
FF00::/8 多播地址
FE80::/10 本地链路地址