Getting started as a contributor
================================

This brief note would serve as a reference for a developer looking to
start-up on the contributing to OpenSDN platform.

PLEASE NOTE: Until 2023, OpenSDN was named “Tungsten Fabric” and
before 2019 it had title “OpenContrail”. There are still several 
references to the old name in the code and other utilities. 

1. Prerequisites
----------------

Before contributing you will need next accounts:

1. a `GitHub <https://github.com>` account with publicly available e-mail
(Settings -> Public profile -> Public e-mail);

2. a `Gerrit <https://gerrit.opensdn.io>` account linked to the GitHub
account (using the GitHub account credentials).

2. Git installation and configuration
-------------------------------------

2.1 Installation of the Git version control system
---------------------------------------------------
Next programs will be needed: git command and OpenSSH server.
For example, on RPM based systems (CentOS, Fedora, etc) they can be installed with
these commands:

   ::

        sudo yum update 
        sudo yum install git
        sudo yum install git-core
        sudo yum install openssh-server

2.2 Generation of an SSH key
----------------------------

If an SSH key was already generated, then this step can be omitted.

Ref:
https://help.github.com/articles/adding-a-new-ssh-key-to-your-github-account

   ::

        ssh-keygen -t rsa -b 4096 -C "<username@someone.com>" //press "Enter" every time you see question
        eval $(ssh-agent -s)
        ssh-add ~/.ssh/id_rsa

Where "<username@someone.com>" corresponds to your GitHub email address.


2.3 Add SSH key to GitHub account
---------------------------------

1. Run `GitHub <https://github.com>` web page in a browser. 

2. Go to profile and select settings.

3. Choose SSH and GPG keys -> New SSH key.

4. Give a title for the key in "Title" field.

5. Copy the public SSH key (for example, obtained from step 2.2):

   ::

        cat ~/.ssh/id_rsa.pub

6. Paste the public SSH key into "Key" field.

7. Add SSH key.

8. Make sure that in GitHub settings in Public profile you have public email
(the one you will add to global git config). This is required for the correct
operation of the Gerrit (see section 1.1).


3. Setting up Gerrit for committing code changes for a review
-------------------------------------------------------------

Reviewing process is organized using Gerrit system and
"git review" command, :ref:`Ref1` , :ref:`Ref2`.

3.1 Setup SSH access
--------------------

1. Log into your `OpenSDN Gerrit account <https://gerrit.opensdn.io>`
using via GitHub credentials.

2. Go to top-right corner -> Settings.

3. Left panel: SSH Keys -> New SSH key.

4. Copy the public SSH key from step 2.3.5.

5. Test the SSH access using your Gerrit account name "sshusername":

   ::

      $ ssh -p 29418 sshusername@gerrit.opensdn.io
   
   After the execution, you should see:

   ::

      **** Welcome to Gerrit Code Review ****

      Hi <sshusername>, you have successfully connected over SSH.

   Unfortunately, interactive shells are disabled. 
   To clone a hosted Git repository, use:

   ::

      git clone ssh://sshusername@gerrit.opensdn.io:29418/REPOSITORY_NAME.git
   
   After the execution, you should see:

   ::

      Connection to hostname closed.

   Available OpenSDN repositories are listed `here <https://gerrit.opensdn.io/admin/repos>`.

3.2 Pushing code changes for a review
-------------------------------------

1. Install git-review utility. For example, on RPM-based systems run the
command:

   ::

      sudo yum install git-review
   
   Or it can be installed using pip `Ref2`_:

   ::

      sudo pip install git-review

2. Configure Gerrit:

   ::

      git config --global user.email username@someone.com
   
   where "username@someone.com" is your email address that is publicly visible on
   your GitHub profile page.

3. Clone the repo where changes need to be committed

   ::

      git clone
      ssh://sshusername@gerrit.opensdn.io:29418/REPOSITORY_NAME.git
   
   where "sshusername" is your OpenSDN Gerrit account name and "REPOSITORY_NAME"
   is the name of an OpenSDN repository (e.g., tf-dev-env, tf-controller, etc).

4. Commit the changes

   ::

      git commit -m "<commit-note>" -s

   "-s" option is needed to sign a commit with your own e-mail and digital
   signature.

5. Push the locally committed changes up for review

   ::

      git review

   In case of successfull execution of the command, you will see:

   ::

      remote: SUCCESS

NOTE. If any of the steps above have raised questions, you can raise an issue on:
https://where.to.raise.an.issue, contact the reponsible resp@opensdn.io or ask
it using in Discord opensdn-discord.

4. References
-------------

.. _Ref1: https://gerrit-review.googlesource.com/Documentation/user-notify.html

.. _Ref2: https://docs.opendev.org/opendev/git-review/latest/installation.html

