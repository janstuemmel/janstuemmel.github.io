A quick reference how to modify the bash prompt, using the **PS1** environment variable. This is useful for presentations for example.

## Basic usage

Just modify the PS1 environment variable.

```sh
export PS1="hello world > "

# the default on most systems
export PS1="\u@\h:\w\$ "
```

## Some useful variables

| Var  | Result                           |
| ---- | -------------------------------- |
| `\u` | Username                         |
| `\h` | Hostname                         |
| `\w` | Working directory                |
| `\W` | Last part of working directory   |
| `\$` | **#** if root, else **$**        |
| `\A` | Time (hh:mm)                     |
| `\t` | Time (hh:mm:ss)                  |


...there are a few more.

## Make it persistent

Just put it in your `~/.bashrc`

```sh
echo export PS1="\u@\h:\w\$ " >> ~/.bashrc
```
