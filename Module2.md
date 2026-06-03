用Python學習網路資訊安全 
封包偽裝大師+流量分析偵探





研究動機
在資訊安全實作1中，已經練習了關於基礎的網路及密碼安全，接下來，繼續練習課程及實作練習，學習基礎資安能力，為未來學習更進階的資安技術打下基礎。

作業設備架設網路概念及虛擬機（ＶＭ）
安全性
進行資安實驗時，務必使用獨立的網路環境（如本地 虛擬機VM 或封閉網段），避免影響學校或家中網路。
測試時避免攻擊公共網路或未授權系統，以免違法。
方法
低成本方案（適合學生）
使用 免費的虛擬機 + 開源工具（Kali Linux、Wireshark．．）

綜合以上
使用 筆電x2 + 數個虛擬機（VM）
虛擬機軟體使用VirtualBox
虛擬機軟體上跑Kali Linux(一種Linux作業系統，專門用來資安的實驗用的)
路由器(router) x1，只使用封閉區域網路，不上網際網路

另一台電腦CPU有8核心,記憶體有16GB，執行四個虛擬機，以有線網路連到路由器
我的電腦只執行一個虛擬機，以無線網路連到同一個路由器
路由器不連上網際網路，形成一個封閉的測試網路
每個虛擬機是一台Kali Linux的機器，分別有自已的網路地址 (192.168.1.x)
通常電腦上網會是用被動態分配網址
動態主機設定協定（英語：Dynamic Host Configuration Protocol，縮寫：DHCP）
本實驗為了方便驗證及觀察，192.168.1.x 是由另一台電腦手動在每個虛擬機設定的不同的靜態(Static)網址
每個電腦的網址一定要不同，以免衝突，如同每人的家的住址都不同。















小實驗1：如何確認自己電腦（虛擬機）的網路地址

$ ifconfig開啟Kali Linux之後，在終端機裡打下列指令，在inet後的即為目前的網路地址
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.10  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::a00:27ff:fe81:d6da  prefixlen 64  scopeid 0x20<link>
        ether 08:00:27:81:d6:da  txqueuelen 1000  (Ethernet)

小實驗2：如何確認電腦的網路連接正常

由於我們知道其他電腦的網址
$ ping 192.168.1.20開啟Kali Linux之後，另一台在終端機裡打下列指令 ping我的電腦
└─$ ping 192.168.1.20
PING 192.168.1.20 (192.168.1.20) 56(84) bytes of data.
64 bytes from 192.168.1.20: icmp_seq=1 ttl=64 time=6.12 ms
64 bytes from 192.168.1.20: icmp_seq=2 ttl=64 time=4.49 ms
64 bytes from 192.168.1.20: icmp_seq=3 ttl=64 time=4.02 ms
64 bytes from 192.168.1.20: icmp_seq=4 ttl=64 time=3.22 ms
64 bytes from 192.168.1.20: icmp_seq=5 ttl=64 time=2.58 ms
正常連接的訊息像下列，表示另一台電腦的Kali Linux 虛擬機跟我們是連正常，會在一定的時間內回應 (2~6 ms millisecond )資安實驗

<img width="1125" height="832" alt="image" src="https://github.com/user-attachments/assets/d5929cbd-606f-48a2-ac48-5af5fa1d85d3" />


<img width="594" height="446" alt="image" src="https://github.com/user-attachments/assets/887a8e06-5457-46e8-b817-f2ae2e6d275c" />

封包偽裝大師
目標：發送偽造網路封包並模擬簡單 DDoS，展示攻擊影響，記錄目標回應時間。
問題討論：「如果駭客淹沒你的網路，你會怎麼辦？」
比喻：像用假信塞滿郵箱。
詳細步驟：
在我的電腦VM 上執行程式碼。
$ sudo python 4packet_master
註:加 sudo 在最前面是因為Linux作業系統把使用某些網路功能當成”管理員”權限，sudo 可以解釋成 super user do 


<img width="1361" height="704" alt="image" src="https://github.com/user-attachments/assets/2bc2d8db-6b2c-4299-8805-5c91b3c21529" />

本程式使用假網址(其實有可能被發現真的網址)，然後對TeacherVM1傳送大量封包，企圖攻擊該電腦
然後收集該電腦的反應時間，由此觀察分析這種攻擊行為是否會使被攻擊電腦變慢


<img width="999" height="676" alt="image" src="https://github.com/user-attachments/assets/150d45a7-f0c7-4288-8d3d-f5501463a090" />

<img width="1366" height="638" alt="image" src="https://github.com/user-attachments/assets/99a233d9-d34e-46fc-b3a7-36d3690ff126" />


本節心得 : 由數據分析(文字及圖)，大量的假封包，已經讓TeacherVM1電腦的反應時間變慢，再用另一台電腦或Terminal 去ping TeacherVM1，的確發現反應的時間變長，甚至直接不回應(unreachable…)。

流量分析偵探
目標：監控並分析流量，生成動態熱圖。
加入網路協議分析，展示流量分佈。
問：「網路流量能洩漏什麼秘密？」
比喻：像偵探追蹤腳印。
詳細步驟：
在我的電腦Kali Linux VM 上執行程式碼。
$ sudo python 5traffic_detective.py



本節心得: 如果駭客能夠分析我們的電腦的各種網路封包及流量，駭客就有機會利用各種方式，得知我們的網路足跡，例如我們常上的網站，看的影片等等。之前曾聽說有網站被破解，就是可能用上述所有的方法去做的，或者是說他們盜取我們的個人資料之後，可以分析之後把它當成一種情報去販賣給其他的業者或者是商家。

另外這裡面的分析有各種不同的網路協議，例如T C P或者U D P等等目前對這個協議所知有限。









反思與收穫
在這次的實驗中，讓我親身體驗到網路攻擊到底是如何形成的，進行DDoS攻擊我另一台電腦的虛擬機的時候，甚至沒有反應，我想應該就是ddos的攻擊使他當機了，導致了無法反應。在這之後我更發現了網路安全的重要性，如果沒有適當的加強防護機制，很可能被輕易破解，學習如何製作保護機制也是我之後可以探討的問題。










參考資料
快速linux指令教學（mac, windows, ubuntu...等等適用）｜工程師必備技能
快速linux指令教學（mac, windows, ubuntu...等等適用）｜工程師必備技能
拯救資工系學生的基本素養—SSH & SCP 連線到遠端機器  
