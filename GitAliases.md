# [:leftwards_arrow_with_hook:](/README.md) | Personal Git Aliases

```properties
[alias]
    #########################
    #     Meta Commands     #
    #########################
    #########################

    ecfg = ! code ~/.gitconfig
    # TODO: name = config --get user.name

    # show all aliases
    aliases = ! git config --global --get-regexp ^alias\\. | sed -e s/^alias\\.// -e s/\\ /\\ =\\ / | sed -e s/$/\\\\n/
    aliases-all = ! git config --get-regexp ^alias\\. | sed -e s/^alias\\.// -e s/\\ /\\ =\\ / | sed -e s/$/\\\\n/

    #########################
    #      Short-hands      #
    #########################
    #########################

    co = checkout
    st = status
    com-m = commit -m
    sub-up = submodule update --init --recursive
    update-repo = ! git fetch --prune && git pull && git sub-up
    commit-empty = commit --allow-empty -m \"Intentionally Empty Commit\"
    ## Needs Git >= 2.22 
    push-new = ! git push --set-upstream origin $(git branch --show-current)

    #########################
    #   Branch Management   #
    #########################
    #########################

    new-branch = "!f() { tmp=\"${@}\"; name=\"${tmp// /_}\"; git checkout -b ${name}; }; f"
    # TODO: new-tagged-branch = 

    # TODO: This could be done better
    # interactive branch management
    ## change branch
    chbri = "!f() { git p-selbri "$@"; if [[ -f "~/.tmpgit" ]]; then echo ""; branch=$(cat ~/.tmpgit); rm ~/.tmpgit; git co $branch; fi; }; f"
    ## delete branch
    dlbri = "!f() { git p-selbri "$@"; if [[ -f "~/.tmpgit" ]]; then echo ""; branch=$(cat ~/.tmpgit); rm ~/.tmpgit; git branch -d $branch; fi; }; f"
    ## force delete branch
    fdlbri = "!f() { git p-selbri "$@"; if [[ -f "~/.tmpgit" ]]; then echo ""; branch=$(cat ~/.tmpgit); rm ~/.tmpgit; git branch -D $branch; fi; }; f"

    # "p-" means this is private and should't be used outside other commands
    # the reason for this is that it mutates the internal "state" by writing to a file
    # writes to "~/.tmpgit" file 
    p-selbri ="!f() { selSym=\">>\"; tmp=$(git branch --list); tmp=($(echo \"$tmp\" | sed \"s/\\* \\{1,\\}/$selSym/g\")); if [[ ${#tmp[@]} -gt 1 ]] ; then tmp2=(); for branch in ${tmp[@]}; do if [[ $branch =~ ^$selSym ]]; then selected=${branch//$selSym/}; echo \"Current Branch: $selected\"; else if [[ ${branch^^} =~ ${1^^} ]]; then tmp2+=($branch); fi; fi; done; echo \"Select Branch:\"; for i in ${!tmp2[@]}; do echo \"  $i - ${tmp2[$i]}\"; done; read -p \"Index: \" index; if [[ $index -ge 0 && $index -lt ${#tmp2[@]} ]]; then echo \"Selected '${tmp2[$index]}'. Writing into ~/.tmpgit\"; echo ${tmp2[$index]} > ~/.tmpgit; else echo \"Invalid index: $index\"; fi; else echo \"No Local Branches To Select From\"; fi; }; f"

    # Get banhes that are no longer tracking on the remote
    gone-branches = ! git branch -v | grep '\\[gone\\]' | sed -e s/*//g | awk '{print $1}'
    # Delete branches that no longer track the remote
    delete-gone-branches = "!f() { if ! [[ "${1,,}" =~ "(-f|-y)" ]]; then echo "Branches to remove:"; git gone-branches; echo ""; echo "To remove the abvoe branches use -f or -y flag"; else for branch in $(git gone-branches); do git branch -D $branch; done; fi; }; f"

    #########################
    #         Other         #
    #########################
    #########################

    # peek at the last n commits on this branch
    # peek-log = "!f() { if [[ $1 =~ ^[0-9]+$ ]]; then numCommits=$1; fwdParams=\"${@:2}\"; else numCommits=10; fwdParams=$@; fi; echo $numCommits; echo $fwdParams; git log --oneline --decorate --format=format:'> %C(bold yellow)%h%C(reset) - %C(bold blue)[%><(20,trunc)%an]%C(reset) - %C(white)[%<(50,trunc)%s]%C(reset) %C(dim green)[%ar]%C(reset)' -n $numCommits $fwdParams; }; f"
    peek-log = log --oneline --decorate --format=format:'> %C(bold yellow)%h%C(reset) - %C(bold blue)[%><(20,trunc)%an]%C(reset) - %C(white)[%<(50,trunc)%s]%C(reset) %C(dim green)[%ar]%C(reset)' -n

    # tree view
    # tree = log --graph --abbrev-commit --decorate --format=format:'%C(bold blue)%h%C(reset) - %C(bold green)(%ar)%C(reset)%C(bold yellow)%d%C(reset)%n''          %C(white)[%<(50,trunc)%s]%C(reset) %C(dim white)- [%<(20,trunc)%an]%C(reset)'

    #########################
    #        Diffing        #
    #########################
    #########################

    # TODO:
    # diff-all = difftool --dir-diff --tool=bcomp-all
    # diff-dir = difftool --dir-diff --tool=bcomp
```