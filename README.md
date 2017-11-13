# Transitioning from an Ubuntu VirtualBox Environment to Windows using WSL

After arriving home with my new Dell XPS, I immediately plugged in my Ubuntu installation USB stick and attempted to boot up Linux. To my disappointment, my laptop didn't get along with its new operation system. It wouldn't sleep or wake up, and it ran hot to the touch, even after playing with sleep settings and closing the lid. After a week of digging through the internet to find solutions and thinking I had bricked it a few times, I determined that Ubuntu just wasn't ready for this newer model. I restored to factory settings, installed VirtualBox, and booted Linux into it.

There's nothing wrong with VirtualBox, and it mostly met my needs. But it was the little things that bothered me about it. Even with guest additions and giving the machine a lot of my computer's resources, the graphics (display mostly) were pretty glitchy. In addition, I'd bought my computer to leverage its resources onto some CPU, RAM intensive projects, or, at least, it was an improvement over its predecessor, and I always felt like I wasn't getting the most out of it. Lastly, from what I could gather, I couldn't just shut the lid and walk away with the VB going. After pulling my computer of my bag, I would find it to be anywhere from warm to hot and with a significant battery loss. I accepted these minor pains since I had no alternative.

After some time, I heard about WSL, or the Windows Subsytem for Linux or Bash on Windows or Ubuntu on Windows, whatever the hell you want to call it, but at the time it was still in development and only in beta. I tried it out on my PC, and it seemed legit even if a bit off with the change in directories and weird errors I'd get occasionally. Even then, it was in beta, you had to change some settings on your computer to enable it, and it still seemed like it needed work.

With the most recent haul of Windows updates, it's out of beta and anyone can give it a try through the Windows Store. With the aches and pains of working through a VM starting to wear on me, I decided to give it a try. Here I document settings I had to change, things I installed, and ways to get through problems that were mostly a lack of familiarity or understanding of Windows. Hopefully this will help someone else with a similar conundrum work through making a transition of their own.

## Setup

I'm a Python developer that hasn't made the leap to using a more heavyweight IDE yet. To really decide if I would want to stick with a Windows setup using WSL, I wanted to be able to use the same tools with the same capabilities as my Ubuntu VM setup. To list them out:

- Use Sublime Text 3 with all the same packages.
- Use the same command line aliases.
- Utilize relational databases just as easily as on Linux.
- Use Jupyter notebooks as easily as Linux.
- Set up Python environments easily.

I think that was a fairly modest list of demands to Windows.

## Installation

This is pretty easy. All you have to do is search 'Ubuntu' in the Windows Store and it will provide directions for what you need to do to start using WSL. READ THE DESCRIPTION. You have to change one little setting through the Control Panel before you start using it.

After performing a restart, you're ready to start using WSL. Open a terminal and you immediately have most of the same commands and tools available to you as on a normal Ubuntu terminal. It comes pre-loaded with Python 3.5. _Python 2.7 does not come installed_. Additionally, you'll need to follow the Ubuntu directions for downloading Python3.6. I used this forum to do that: https://askubuntu.com/questions/865554/how-do-i-install-python-3-6-using-apt-get There, you can alias your Python3 command for Python3.6. I haven't gone through the trouble of downloaded Python 2.7 yet, but I imagine the process is similar. Additionally, you'll need to download pip or pip3.

Next, I attempted to download Sublime through Bash. This was a mistake. Though it will successfully download, it will not be able to execute. You will have to download Sublime through Windows. Unfortunately, WSL does not provide a command or path to Bash to execute the program, so you will have to provide an alias to your bashrc, which is in the typical Ubuntu home directory:

    alias subl='"/mnt/c/Program Files/Sublime Text 3/subl.exe"'

Source it to take effect, and you will now be able to run Windows Sublime through bash. Neat!
When running subl in the home directory, it will show a long list of directories and files which is kind of gross. I decided to make an alias to switch to directories closer to Windows directories:

    alias kurt='cd /mnt/c/Users/kurtrm/projects'

Now, when running commands like ```subl .```, it will act more or less as expected.

Next, we'll want to get the Linter up and working. Additionally, changing settings is fairly straightforward and mostly similar to Linux. The major difference was changing the default font. I wanted to use Droid Sans Mono Slashed (http://www.cosmix.org/software/), so I downloaded it, installed it, and verified it was in my Windows fonts directory, and attempted to use it. I couldn't get it to work for the life of me. I changed the name a hundred times, tried redownloading, etc. Finally, after clicking on properties, there is a Read-Only property that was checked, and another box below it saying that Windows was blocking me from using it since it was downloaded from the internet. After correcting those issues, the font worked.

Getting the Linter going took some work, and a better way likely exists to get this done. I downloaded Sublime Linter, SublimeLinter-flake8, and Jedi. In order to use flake8 and Jedi, you need to provide paths to executables. Unfortunately, with my limited understanding of Windows, this took a great deal of effort. I didn't know where Python was on Windows, but I knew that I wanted to work in Anaconda and that it has its own Python executable. I downloaded this and located the Python executable. I then opened a notebook and installed flake8 (```!pip install flake8```). I'm used to a bin directory, as well as forward slashes, so I had a hard time finding which path to supply to my sublime settings and how to write it out. This is what you'll need to provide, or something similar if you're not using Anaconda:

#### flake8
Provide the path to the _directory_, not all the way to the executable itself. In your SublimeLinter settings you'll find the following section. Put in the path to the scripts folder here, being sure to secape the backslashes:

        "paths": {
            "linux": [],
            "osx": [],
            "windows": [
                "C:\\Users\\kurtrm\\Anaconda3\\Scripts"
            ]
        },

#### Jedi
Similar as above for the python executable and packages directory:

    {
        "python_interpreter": "C:\\Users\\kurtrm\\Anaconda3\\python.exe",
        "python_package_paths": ["C:\\Users\\kurtrm\\Anaconda3\\Lib\\site-packages"]
    }

You should be all set with Sublime after this. If you get some weird errors, you might have to go to this directory and ensure there is a trailing line at the end of documents:

    C:\Users\kurtrm\AppData\Roaming\Sublime Text 3

(I guess this is where your settings are stored...)

Lastly, the only other issue I had was postgres. I know that it comes installed on Ubuntu, but I wasn't entirely sure it came installed on WSL, so I went ahead and downloaded it anyway. I was unable to use my terminal commands ```psql``` or ```sudo -u postgres psql postgres```... because the server wasn't running. Run ```sudo service /etc/init.d/postgresql start``` to get it running, and I recommend Googling methods to make the server start automatically if you want to. I did this only because I never had to do mess with the server on Ubuntu. You will also likely need to create a super user (```CREATE USER <username> WITH SUPERUSER;```) and give it a password.

Everything else was cake. Using a Python environment is exactly the same, and, rather than using a command line argument to run a Jupyter notebook, I simply use the Anaconda Navigator, opinions aside. 

The only other thing worth mentioning is the color scheme. Much like the Command Prompt, you can customize texts and backgrounds, but you will have to make some bashrc edits for other aspects of the terminal. The color scheme I chose clashes with IPython text and warnings, so I'll have to go in and change that in the bashrc.

I hope you found this helpful, and if you have anything else to add for those needing to make this transition, feel free to leave a comment!
    
