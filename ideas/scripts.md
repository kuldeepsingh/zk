---
title: Ideas - scripts
date: February 10, 2025
tags: []
---
### Build Tags/Cscope for datapane 
### ------------------------------

#!/bin/sh -e

case "$1" in
	"cscope")
        rm -rvf cscope.files
        find "$PWD" -type d \( -name .svn -o -name CVS -o -name .hg -o -name .git \) -prune -o -regex '.*/.*\.\(c\|cpp\|h\\|S\|hh\|cc\|py\)$' -not -type l -print >> cscope.files
		cscope -q -b -f cscope.out
		;;
	"gtags")
		cat cscope.files | gtags -i -f -
		;;
	"tags")
		rm -f tags
		cat cscope.files | xargs ctags -a
		;;
	"etags")
		rm -f TAGS
		cat cscope.files | xargs etags -a
		;;
	*)
		echo "Invalid mode: $1"
		print_usage
		;;
esac

