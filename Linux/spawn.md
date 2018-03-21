```shell
#!/usr/bin/expect
spawn ssh xxxx
expect "*password:"
send "***"
interact
```
