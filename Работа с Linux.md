###### Находит и завершает процесс, использующий указанный порт.
```python
import psutil
def find_and_kill_process(port):    
        connections = psutil.net_connections(kind='inet')  
    for conn in connections:  
        if conn.laddr.port == port:  
            pid = conn.pid  
            if pid:  
                process = psutil.Process(pid)  
                process.terminate()  
                print(f"Процесс {pid}, занимающий порт {port}, был завершен.")
```

###### Мониторинг UDP трафика на указанном порту. Метод запускает утилиту tcpdump и парсит ее вывод. Вывод tcpdump в консоль выглядит так:        20:27:59.599259 enx344b50000000 In  IP 213.87.134.230.56028 > 172.16.0.150.5555: UDP, length 1024
```python
import subprocess
def monitor_udp_traffic(self):  
    try:  
        cmd = ["sudo", "tcpdump", "-i", "any", "-n", f"udp and port {self.local_port}", "-l"]  
        print(f"Running command: {' '.join(cmd)}")  
  
        process = subprocess.Popen(cmd, stdout=subprocess.PIPE, stderr=subprocess.PIPE)  

        while not stop_event.is_set():  
            pattern = re.compile(rf"In.*{re.escape(monitoring_ip)}")  
            output = process.stdout.readline()  
            if output:  
                output_str = output.decode().strip()  
                if pattern.search(output_str):  
                    is_receiving_traffic = True  
                      
        process.terminate()  
    except Exception as e:  
        print(f"An error occurred: {e}")
```


