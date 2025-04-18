# Git Basics      
### Version Tracking for Code     

Git is a code version control code. See [an in progress/tbd article about git workflow] for our team's workflow; otherwise, this is just an introduction to Git.

## Key Terms
**Git:** A commonly used version control software.     
**Git Bash:** A terminal in which to run git from. Open on windows by pressing the `[Windows]` key, typing in `"git bash"`, and hitting enter. You should end up with something like this:       

<img alt="Git Bash Terminal" src="../Assets/Git_Bash_Terminal.png" width="373" height="210">

Here, note that the stuff before the at (@) sign is the name of the user on the computer (for me, `flow`), and the stuff after the at sign is the name of the computer (for me, `Laptop-Laptop-Laptop`).

**Github:** A place to store repositories (repos), or projects. Note that github is not the only place where repositories can be stored, it is simply the most common place. While git is open source, github is run by Microsoft.      
**Github Desktop:** A GUI alternative to git.

---

## Basic Usage

#### Terminal Commands
- `cd`: stands for "change directory," and use `cd` to move around your file structure by typing `cd [folder name]`. For example, to move the terminal (think of it like a pointer) state from being in `C:/lorem/ipsum/delta` to `C:/lorem/ipsum/delta/eta`, use `cd eta`. Also, to go back to a parent folder (e.g. from `C:/Alice/Bob/Charlie` to `C:/Alice/Bob`), use `cd ..`.      
- `dir`/`ls`: lists all folders/files in the current directory. For example, from the [840 2025 codebase](https://github.com/Aragon-Robotics-Team/frc-2025):

<img alt="Git Bash Terminal (ls/dir command)" src="../Assets/Git_Bash_Terminal_DIR-LS.png" width="396" height="200">

Note that `ls` provides color highlighting based on file type.     
- `clear`: clears the terminal screen by pushing all output upwards. Use this if the screen gets too cluttered.


#### Git Commands    
Note that these commands are now git specific. The basic syntax of all these commands is `git [command name] [arguments]` (e.x. `git switch some-branch-name`).    
- `git commit -m "[some message]"`: Makes a git commit with the message being `[some message]`. The message is what appears as the small text in the github repository, so remember to make your message concise! Note that making a commit is essentially like placing a landmark -- it marks where in the code your repository is at some specific time. To add files to make a commit, use:     
- `git add [files]`: Adds (stages) files to be committed. To add your whole repository to be committed, use `git add .` where the `.` here means "everything in this folder". To then push any changes, use:    
- `git push`: Pushes any commits to a remote, usually a github repository. This is what you do to "sync" the online github code repository to the one on your computer.

- `git pull`: Pulls any commits from the remote and merges them with your local repo. Use this whenever there are some changes online that you want to get on your local computer. For example, if Alice pushes some code (using `git push`) to some repo and Bob wants that code, he must run `git pull` to essentially update his local code to be the same as the code online.     

In summary, if nothing else is gleaned, whenever you make changes that you want to save and push online, run `git add .`, `git commit -m "[your message]"`, and `git push`. 



---

## Practice  
Make your own repo, clone it onto your own computer (`git clone [url]`), and practice making changes, and pushing them.

