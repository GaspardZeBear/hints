# pktmon : tcpdump for windows

~~~
pktmon filter add -p 1961
pktmon filter list
pktmon start -c -m 
  -c : capture
  -m r : realtime
  -m muti-file : to an .etl file
  --pkt-size n  : (0 : all)
  -f file : .etl file

etl2txt <file.etl> -v -t : generate .txt
~~~

