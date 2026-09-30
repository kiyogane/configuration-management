## Задача 1

cd /etc
grep -oE '^[^:]+' passwd | sort

## Задача 2

cat /etc/protocols | awk '{print $2, $1}' | sort -k1,1nr | head -n 5

## Задача 3

cd ~
nano banner

```
#!/bin/bash

if [ $# -ne 1 ]; then
    echo "Использование: $0 \"текст\"" >&2
    exit 1
fi

text="$1"
line=$(printf '%*s' $((${#text} + 2)) '' | tr ' ' '-')

printf '+%s+\n' "$line"
printf '| %s |\n' "$text"
printf '+%s+\n' "$line"
```

chmod +x banner
./banner "Hello from RTU MIREA!"

## Задача 4

nano hello.c
```
#include <stdio.h>
int main(void) { printf("hello, world\n"); return 0; }
```

nano identifier

```
#!/bin/bash

if [ $# -ne 1 ]; then
    echo "Использование: $0 файл" >&2
    exit 1
fi

if [ ! -f "$1" ]; then
    echo "Файл $1 не найден" >&2
    exit 1
fi

grep -owE '[A-Za-z_][A-Za-z0-9_]*' "$1" | sort -u | paste -sd' '
```

chmod +x identifier
./identifier hello.c

## Задача 5

nano reg
```
#!/bin/bash

if [ $# -ne 1 ]; then
    echo "Использование: $0 файл" >&2
    exit 1
fi

if [ ! -f "$1" ]; then
    echo "Файл $1 не найден" >&2
    exit 1
fi

chmod 755 "$1"
cp "$1" /usr/local/bin/
```
chmod +x reg
sudo ./reg banner

cd в любую папку 
banner "Hi"

## Задача 6

nano check

```
#!/bin/bash

dir="${1:-.}"

for file in "$dir"/*.{c,js,py}; do
    [ -f "$file" ] || continue

    case "$file" in
        *.py) pattern='^[[:space:]]*#' ;;
        *)    pattern='^[[:space:]]*(//|/\*)' ;;
    esac

    if head -n 1 "$file" | grep -qE "$pattern"; then
        echo "$file: комментарий есть"
    else
        echo "$file: комментария нет"
    fi
done
```

chmod +x check

echo "// test" > a.c
echo "int x;" > b.c
echo "# test" > c.py
echo "print(1)" > d.py

./check


## Задача 7

nano duplicates
```
#!/bin/bash

if [ $# -ne 1 ]; then
    echo "Использование: $0 каталог" >&2
    exit 1
fi

if [ ! -d "$1" ]; then
    echo "Каталог $1 не найден" >&2
    exit 1
fi

find "$1" -type f -exec md5sum {} + | sort | uniq -w32 --all-repeated=separate
```

chmod +x duplicates
mkdir test && cd test
echo "one" > a.txt
echo "one" > b.txt
echo "two" > c.txt
cd ..
./duplicates test

## Задача 8

nano arch

```
#!/bin/bash

if [ $# -lt 1 ]; then
    echo "Использование: $0 расширение [каталог]" >&2
    exit 1
fi

ext="$1"
dir="${2:-.}"

if [ ! -d "$dir" ]; then
    echo "Каталог $dir не найден" >&2
    exit 1
fi

find "$dir" -maxdepth 1 -type f -name "*.$ext" -print0 | tar -cf archive.tar --null -T -
```
chmod +x arch

./arch txt
Проверка: tar -tf archive.tar


## Задача 9

nano tabul
```
#!/bin/bash

if [ $# -ne 2 ]; then
    echo "Использование: $0 входной_файл выходной_файл" >&2
    exit 1
fi

if [ ! -f "$1" ]; then
    echo "Файл $1 не найден" >&2
    exit 1
fi

sed 's/    /\t/g' "$1" > "$2"
```
chmod +x tabul

printf 'a    b    c\n' > input.txt
./tabul input.txt output.txt
cat -A output.txt


## Задача 10

nano empty
```
#!/bin/bash

if [ $# -ne 1 ]; then
    echo "Использование: $0 каталог" >&2
    exit 1
fi

if [ ! -d "$1" ]; then
    echo "Каталог $1 не найден" >&2
    exit 1
fi

find "$1" -maxdepth 1 -type f -name '*.txt' -empty -printf '%f\n'
```
chmod +x empty

mkdir test
touch test/a.txt test/b.txt
echo "text" > test/b.txt
./empty test