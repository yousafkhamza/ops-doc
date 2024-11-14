# Bash Scripting Interview Questions and Answers

## Command Comparisons

### 1. File Operations Commands
| Command | sed | awk | cut |
|---------|-----|-----|-----|
| Primary Use | Stream editing | Pattern scanning/processing | Extract sections |
| Functionality | Line manipulation | Full programming language | Column extraction |
| Complexity | Moderate | High | Low |
| Example | `sed 's/old/new/'` | `awk '{print $1}'` | `cut -d',' -f1` |

### 2. Process Monitoring Commands
| Command | ps | top | htop |
|---------|-----|-----|------|
| Display | Static | Dynamic | Interactive |
| Resource Usage | Low | Medium | Medium |
| UI | Simple | Basic | Advanced |
| Sorting | Limited | Yes | Flexible |

### 3. Text Search Commands
| Feature | grep | find | locate |
|---------|------|------|--------|
| Search Type | Content | Files/Dirs | Database |
| Speed | Fast | Slow | Very Fast |
| Freshness | Real-time | Real-time | Database update |
| Recursion | Optional | Default | N/A |

## Basic Scripts and Concepts

### 1. Variable Declaration and Comparison
```bash
#!/bin/bash

# Variable declaration comparison
var1=value     # Simple assignment
declare -i num=5    # Integer declaration
readonly const=10   # Constant declaration
declare -A map     # Associative array

# String comparison
if [ "$str1" = "$str2" ]     # POSIX compatible
if [[ "$str1" == "$str2" ]]  # Bash extended

# Numeric comparison
if [ "$num1" -eq "$num2" ]   # POSIX compatible
if (( num1 == num2 ))        # Bash arithmetic
```

### 2. Loop Comparisons
```bash
# For loop variations
for i in {1..5}; do          # Sequence
    echo $i
done

for ((i=0; i<5; i++)); do    # C-style
    echo $i
done

# While loop variations
while [ "$count" -lt 5 ]; do  # Test command
    echo $count
    ((count++))
done

while read -r line; do        # File reading
    echo "$line"
done < input.txt
```

## Intermediate Scripts

### 1. Error Handling Comparison
```bash
# Method 1: Basic error handling
command || { echo "Error"; exit 1; }

# Method 2: Trap-based
trap 'echo "Error on line $LINENO"' ERR
trap 'cleanup_function' EXIT

# Method 3: Complete error handling
set -euo pipefail
function handle_error() {
    local line=$1
    local command=$2
    echo "Error in command '$command' on line $line"
}
trap 'handle_error ${LINENO} "$BASH_COMMAND"' ERR
```

### 2. Function Styles Comparison
```bash
# Style 1: Simple function
function_name() {
    local var1=$1
    echo "$var1"
}

# Style 2: Documented function
##################################
# Description of function
# Arguments:
#   $1 - Description of arg1
# Returns:
#   0 if successful, 1 on error
##################################
function function_name() {
    local arg1=$1
    [[ -z "$arg1" ]] && return 1
    echo "$arg1"
    return 0
}

# Style 3: Return value handling
get_value() {
    local __resultvar=$1
    local result="some value"
    eval $__resultvar="'$result'"
}
```

## Real-World Scenarios

### 1. Log Analysis Script
```bash
#!/bin/bash
# Scenario: Analyze web server logs for errors and traffic patterns

# Method 1: Using awk
analyze_logs_awk() {
    awk '
    /ERROR/ {errors++}
    /POST/ {posts++}
    /GET/ {gets++}
    END {
        print "Errors:", errors
        print "POST requests:", posts
        print "GET requests:", gets
    }' "$1"
}

# Method 2: Using grep and wc
analyze_logs_grep() {
    local logfile=$1
    echo "Errors: $(grep -c ERROR "$logfile")"
    echo "POST requests: $(grep -c "POST" "$logfile")"
    echo "GET requests: $(grep -c "GET" "$logfile")"
}

# Usage comparison
time analyze_logs_awk access.log
time analyze_logs_grep access.log
```

### 2. System Monitoring Script
```bash
#!/bin/bash
# Scenario: Monitor system resources and alert

monitor_resources() {
    # CPU usage
    cpu_usage=$(top -bn1 | grep "Cpu(s)" | awk '{print $2}')
    
    # Memory usage
    mem_usage=$(free | grep Mem | awk '{print $3/$2 * 100.0}')
    
    # Disk usage
    disk_usage=$(df -h / | awk 'NR==2 {print $5}' | tr -d '%')
    
    # Alert if thresholds exceeded
    if (( $(echo "$cpu_usage > 80" | bc -l) )); then
        send_alert "CPU usage high: $cpu_usage%"
    fi
    
    if (( $(echo "$mem_usage > 90" | bc -l) )); then
        send_alert "Memory usage high: $mem_usage%"
    fi
    
    if (( disk_usage > 85 )); then
        send_alert "Disk usage high: $disk_usage%"
    fi
}

send_alert() {
    local message=$1
    # Email alert
    echo "$message" | mail -s "System Alert" admin@example.com
    # Log alert
    logger -p user.warning "$message"
}
```

### 3. Backup Script with Different Methods
```bash
#!/bin/bash
# Scenario: Different backup strategies

# Method 1: Simple tar backup
tar_backup() {
    local source_dir=$1
    local backup_file="backup_$(date +%Y%m%d).tar.gz"
    tar -czf "$backup_file" "$source_dir" || return 1
    echo "Backup created: $backup_file"
}

# Method 2: Incremental backup using rsync
rsync_backup() {
    local source_dir=$1
    local backup_dir=$2
    local date_stamp=$(date +%Y%m%d)
    
    # Create hard-link based incremental backup
    rsync -ah --link-dest="$backup_dir/latest" \
          "$source_dir/" "$backup_dir/$date_stamp/"
    
    # Update latest link
    ln -nsf "$backup_dir/$date_stamp" "$backup_dir/latest"
}

# Method 3: MySQL database backup
db_backup() {
    local db_name=$1
    local backup_file="db_backup_$(date +%Y%m%d).sql.gz"
    
    mysqldump --single-transaction \
              --routines \
              --triggers \
              --databases "$db_name" | gzip > "$backup_file"
}
```

### 4. Deployment Script
```bash
#!/bin/bash
# Scenario: Application deployment with rollback

deploy_application() {
    local app_name=$1
    local version=$2
    local deploy_dir="/var/www/$app_name"
    local backup_dir="/var/www/backups"
    
    # Backup current version
    if [[ -d "$deploy_dir" ]]; then
        tar -czf "$backup_dir/${app_name}_$(date +%Y%m%d_%H%M%S).tar.gz" \
            "$deploy_dir" || return 1
    fi
    
    # Deploy new version
    git clone -b "$version" "git@github.com:org/$app_name.git" \
        "${deploy_dir}_new" || {
            echo "Deployment failed"
            return 1
        }
    
    # Atomic swap
    mv "$deploy_dir" "${deploy_dir}_old"
    mv "${deploy_dir}_new" "$deploy_dir"
    
    # Test deployment
    if ! test_deployment "$app_name"; then
        echo "Deployment test failed, rolling back"
        mv "${deploy_dir}_old" "$deploy_dir"
        return 1
    fi
    
    # Cleanup
    rm -rf "${deploy_dir}_old"
    return 0
}

test_deployment() {
    local app_name=$1
    # Add deployment tests here
    curl -f "http://localhost/$app_name/health" > /dev/null 2>&1
}
```

### 5. Log Rotation and Cleanup
```bash
#!/bin/bash
# Scenario: Advanced log management

rotate_logs() {
    local log_dir=$1
    local max_age=$2  # days
    local max_size=$3 # megabytes
    
    # Find and compress old logs
    find "$log_dir" -name "*.log" -type f -mtime +"$max_age" \
        -exec gzip {} \;
    
    # Remove logs exceeding size limit
    find "$log_dir" -type f -size +"$max_size"M \
        -exec bash -c 'echo "Removing large log: {}"; rm {}' \;
    
    # Archive compressed logs older than 30 days
    find "$log_dir" -name "*.gz" -type f -mtime +30 \
        -exec mv {} /archive/ \;
}

# Usage
rotate_logs "/var/log/myapp" 7 100
```

## Advanced Techniques

### 1. Compare Different IPC Methods
```bash
# Method 1: Named Pipes (FIFO)
mkfifo /tmp/testpipe
producer > /tmp/testpipe &
consumer < /tmp/testpipe

# Method 2: Signals
trap 'echo "Received SIGUSR1"' SIGUSR1
kill -SIGUSR1 $PID

# Method 3: Shared Memory (using temporary files)
tmp_file=$(mktemp)
echo "data" > "$tmp_file"
```

### 2. Performance Comparison
```bash
#!/bin/bash
# Compare different methods of processing large files

# Method 1: Line by line reading
while_read() {
    while read -r line; do
        process_line "$line"
    done < "$1"
}

# Method 2: Batch processing
awk_process() {
    awk '{process_batch($0)}' "$1"
}

# Method 3: Parallel processing
parallel_process() {
    cat "$1" | parallel --pipe process_chunk
}

# Benchmark
time while_read "largefile.txt"
time awk_process "largefile.txt"
time parallel_process "largefile.txt"
```

This comprehensive guide covers the most important aspects of Bash scripting with practical examples. Would you like me to:
1. Add more real-world scenarios?
2. Expand the comparisons section?
3. Include more performance optimization examples?