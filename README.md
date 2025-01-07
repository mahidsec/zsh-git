# Git-Enhanced Zsh Prompt (Speacially for kali-linux)

This document outlines how to enhance your Zsh prompt to dynamically display Git repository status using colors and icons. The Git status appears on the **right side of the terminal** using Zsh's `RPROMPT`.
---
## **Example Output**

### Clean Repository (<span style="color:green;">Green ✔</span>):
```plaintext
┌──(user@machine)-[~/projects/myproject]
└─$                                                                                     main ✔
```

### Repository with Untracked/Unstaged Changes (<span style="color:red">Red ⚠</span>):
```plaintext
┌──(user@machine)-[~/projects/myproject]
└─$                                                                                     main ⚠
```

### Repository with Staged Changes (<span style="color:yellow">Yellow ⚠</span>):
```plaintext
┌──(user@machine)-[~/projects/myproject]
└─$                                                                                     main ⚠
```

---

## **Git Status Function**

The following function dynamically checks the state of your Git repository and displays the branch name along with a status icon:

- **Green ✔**: Clean repository (no changes).
- **Yellow ⚠**: Staged changes.
- **Red ⚠**: Untracked or unstaged changes.

### Add this **Function** at the top of the Prompt Configuration.
```zsh
git_status() {
    branch=$(git symbolic-ref --short HEAD 2>/dev/null || git rev-parse --short HEAD 2>/dev/null)
    [ -n "$branch" ] || return  # Exit if not in a Git repo

    # Check different states
    staged=$(git diff --cached --quiet || echo "+")  # Staged changes
    unstaged=$(git diff --quiet || echo "~")         # Unstaged changes
    untracked=$(git ls-files --others --exclude-standard | wc -l)

    # Determine the state
    if [ "$unstaged" ] || [ "$untracked" -gt 0 ]; then
        echo "%B%F{red}$branch ⚠%f%b"  # Bold and red for untracked or unstaged changes
    elif [ "$staged" ]; then
        echo "%B%F{yellow}$branch ⚠%f%b"  # Bold and yellow for staged changes
    else
        echo "%B%F{green}$branch ✔%f%b"  # Bold and green for clean state
    fi
}
```

---

## **Prompt Configuration**

Update your `.zshrc` to integrate the `git_status` function with the Zsh prompt. The Git status is displayed on the **right side** of the terminal using `RPROMPT`.

### **Code**
```zsh
PROMPT=$'%F{%(#.blue.green)}┌──${debian_chroot:+($debian_chroot)─}${VIRTUAL_ENV:+($(basename $VIRTUAL_ENV))─}(%B%F{%(#.red.blue)}%n'$prompt_symbol$'%m%b%F{%(#.blue.green)})-[%B%F{reset}%(6~.%-1~/…/%4~.%5~)%b%F{%(#.blue.green)}]\n└─%B%(#.%F{red}#.%F{blue}$)%b%F{reset} '
RPROMPT='$(git_status)'
```

---

## **How It Works**

1. **`git_status` Function**:
   - Dynamically checks the Git repository status and determines the state based on:
     - Staged changes.
     - Untracked or unstaged changes.
     - Clean state.
   - Displays the branch name with color-coded icons.

2. **`RPROMPT`**:
   - The `RPROMPT` variable places the Git status on the **right-hand side** of the terminal.

3. **Prompt Appearance**:
   - The left prompt shows the user, host, and current directory.
   - The right prompt dynamically updates based on the Git status.

---

## **How to Apply**

1. Copy the `git_status` function into your `.zshrc` file.
2. Update the `PROMPT` and `RPROMPT` variables as shown above.
3. Reload your Zsh configuration:
   ```bash
   source ~/.zshrc
   ```

Your terminal is now enhanced with dynamic Git status updates!
