# `hist` - Bash History Lookup Tool

"_Bash history is documentation_"

This utility facilitates the interrogation of bash history.

## Setup

Because an infinite history can quickly become unmanagable, I'd recommend using a new file each month. This can be achieved using the following example in your `~/.bashrc`.

```bash
function history_file_get() {
  # return the full path to the current history file
  # the output will update each month - even if your terminal is running for a long time
	echo "${HISTORY_OVERRIDE:-${HOME}/.bash_history/history_$(date +'%Y-%m' "${@}")}"
}

function history_file_append() {
  # append new items to the history file
  local histfile="$(history_file_get "${@}")"
  local histdir="$(dirname "${histfile}")"
  [ ! -d "${histdir}" ] && mkdir -p "${histdir}"
	history -a "${histfile}"
}

function history_file_read() {
  # read items from the history file
  local histfile="$(history_file_get "${@}")"
  local histdir="$(dirname "${histfile}")"
  [ -d "${histdir}" ] && history -r "${histfile}"
}

# don't limit the size of history files
HISTSIZE=-1
HISTFILESIZE=-1

# if this is unset, then you won't have timestamps in your history...
HISTTIMEFORMAT="| %Y-%m-%d %a %l:%M:%S %P |  "

# read last month's history too
history_file_read -d "1 month ago"
history_file_read

PROMPT_COMMAND='history_file_append'
```

## Usage

Out of the box, `hist` will inspect the last 5 months worth of history files, and print matching lines.

The output can be filtered by adding keywords to the command line, only lines that contain all keywords will be displayed.

The following example is taken from a Docker container, having run the setup above.

```bash
root@8798116766c8:~# ./hist read
/root/.bash_history/history_2019-06   (2x)
2019-06-06 15:41:46  |    1x | function history_file_read() {   local histfile="$(history_file_get)";   local histdir="$(dirname "${histfile}")";   [ -d "${histdir}" ] && history -n "${histfile}"; }
2019-06-06 15:41:46  |    1x | history_file_read
```

```bash
root@8798116766c8:~# ./hist read local
/root/.bash_history/history_2019-06   (1x)
2019-06-06 15:41:46  |    1x | function history_file_read() {   local histfile="$(history_file_get)";   local histdir="$(dirname "${histfile}")";   [ -d "${histdir}" ] && history -n "${histfile}"; }
```
