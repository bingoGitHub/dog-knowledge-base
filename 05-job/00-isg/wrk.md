10.2.161.105 /data/wrk


./wrk -c100 -t8 -d60s --latency http://10.2.161.141/testerror/

./wrk -t8 -c100 -d30s --latency https://sg.digitalgd.com.cn/testerror/