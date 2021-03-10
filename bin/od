#!/bin/bash
 
while read line
do
    for ((i=0;i<${#line};i++))
    do
        printf " %02X" \'${line:$i:1}
    done
done < ${1:-/dev/fd/0}
