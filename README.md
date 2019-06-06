# `hist` - Bash History Lookup Tool

"_Bash history is documentation_"

This utility facilitates the interrogation of bash history.

Because an infinite history can quickly become unmanagable, I'd recommend using a new file each month. This can be achieved using the following example in your `~/.bashrc`.

```bash
function history_file_get() {
  # return the full path to the current history file
  # the output will update each month - even if your terminal is running for a long time
	echo "${HOME}/.bash_history/history_$(date +'%Y-%m')"
}

function history_file_append() {
  # append new items to the history file
  local histfile="$(history_file_get)"
  local histdir="$(dirname "${histfile}")"
  [ ! -d "${histdir}" ] && mkdir -p "${histdir}"
	history -a "${histfile}"
}

function history_file_read() {
  # read items from the history file
  local histfile="$(history_file_get)"
  local histdir="$(dirname "${histfile}")"
  [ -d "${histdir}" ] && history -n "${histfile}"
}

# don't limit the size of history files
HISTSIZE=-1
HISTFILESIZE=-1

# if this is unset, then you won't have timestamps in your history...
HISTTIMEFORMAT="| %Y-%m-%d %a %l:%M:%S %P |  "

PROMPT_COMMAND='history_file_append'
history_file_read
```
