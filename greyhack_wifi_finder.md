crypto = include_lib("/lib/crypto.so")
airmon(crypto, "start", "wlan0")
hostComputer = get_shell.host_computer
networks = hostComputer.wifi_networks("wlan0")
result = []
previousPWR = [0]
for network in networks
   parsedItem = network.split(" ")
   item = {}
   item.BSSID = parsedItem[0]
   item.PWR = parsedItem[1]
   item.ESSID = parsedItem[2]
    PWR_int = split(item.PWR, "%")
    if to_int(PWR_int[0]) > previousPWR[0] then
   	 if result.len >= 1 then
   		 result.remove(0)
   		 end if
   	 result.push(item)
   	 BSSID = item.BSSID
   	 ESSID = item.ESSID
   	 previousPWR.remove(0)
   	 previousPWR.push(to_int(PWR_int[0]))
   	 end if
end for
ACKs = 300000 / previousPWR[0]
aireplay(crypto, BSSID, ESSID, ACKs)
wifi_pwd = aircrack(crypto, "/home/" + active_user + "/file.cap")
print(ESSID + ":" + wifi_pwd)
