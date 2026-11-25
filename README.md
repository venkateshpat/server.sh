# server.sh
Bash script for Linux servers. It uses standard Linux utilities and provides the requested CPU, memory, disk, and top-process statistics.

#!/bin/bash

# Server Performance Statistics
# Works on most Linux distributions

echo "========================================"
echo "        SERVER PERFORMANCE STATS"
echo "========================================"
echo ""

# 1. Total CPU Usage
echo "---- CPU Usage ----"

CPU_IDLE=$(top -bn1 | grep "Cpu(s)" | awk '{print $8}' | cut -d. -f1)

if [ -z "$CPU_IDLE" ]; then
    CPU_IDLE=$(top -bn1 | grep "%Cpu" | awk '{print $8}' | cut -d. -f1)
fi

CPU_USAGE=$((100 - CPU_IDLE))

echo "Total CPU Usage: ${CPU_USAGE}%"
echo ""

# 2. Memory Usage
echo "---- Memory Usage ----"

free -h

echo ""
free -m | awk '
    NR==2 {
        used=$3
        total=$2
        free=$4
        percentage=(used/total)*100

        printf "Total Memory : %d MB\n", total
        printf "Used Memory  : %d MB\n", used
        printf "Free Memory  : %d MB\n", free
        printf "Memory Usage : %.2f%%\n", percentage
    }
'

echo ""

# 3. Disk Usage
echo "---- Disk Usage ----"

df -h --total | awk '
    $1=="total" {
        print "Total Disk : " $2
        print "Used Disk  : " $3
        print "Free Disk  : " $4
        print "Disk Usage : " $5
    }
'

echo ""

# 4. Top 5 Processes by CPU Usage
echo "---- Top 5 Processes by CPU Usage ----"

ps -eo pid,comm,%cpu --sort=-%cpu | head -n 6

echo ""

# 5. Top 5 Processes by Memory Usage
echo "---- Top 5 Processes by Memory Usage ----"

ps -eo pid,comm,%mem --sort=-%mem | head -n 6

echo ""

echo "========================================"
echo "        SERVER STATS COMPLETED"
echo "========================================"
