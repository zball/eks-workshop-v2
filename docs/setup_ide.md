
# EC2 Setup
1. Go to EC2 console by typing EC2 in the search box at the top of the console and selecting EC2 from the dropdown services.
2. On the left navigation click on Instances
3. Click Launch instances Button
4. Enter eks-ide-tf under Name and select Amazon Linux from Quick Start tab under Application and OS images 
5. Select t3.large from instance type
6. Click Create new key pair from key pair
7. Enter eks-ide-key in key pair name, select RSA for the key pair type and .pem for private key file format. Click Create key pair button which will generate and download private key on your computer.
8. Take all the default Network settings. In the configure storage change the storage to 80 and click Launch instance button
9. If your laptop is using macOS then make sure you run the following command on the pem file downloaded in the earlier step chmod 400 eks-ide-key.pem


# Visual Studio Code Setup
1. Go to https://code.visualstudio.com/download  site and download the
2. Visual Studio Code for your appropriate environment.
Detail instruction for the installation of Visual Studio can be found at:
macOS https://code.visualstudio.com/docs/setup/mac 
Windows https://code.visualstudio.com/docs/setup/windows 
3. Install Remote Development Extension for Visual Studio Code by going to Remote Development Extension https://marketplace.visualstudio.com/items?itemName=ms-vscode-remote.vscode-remote-extensionpack


## Configure VS Code to connect to EC2
1. Configure ssh config file by going to VS Code Command Palette by clicking Shift + Command + P (macOS) and Ctrl + Shift + P (Windows) and start typing remote to see Remote-SSH: Open SSH Configuration File. Click on Remote-SSH: Open SSH Configuration File
2. VS will present few location for the configuration file, select the one in your /Users (macOS) C:\Users (windows) folder
3. Enter the following in the ssh config file

```
Host eks-ide-tf
  HostName [Hostname of your EC2 that you created in the previous step]
  User ec2-user
  IdentityFile [location where the pem file you downloaded is]/eks-ide-key.pem

  ```

  ## 

  ## Setup EC2 using vscode
  1. Once connected to EC2 from vscode open new terminal and type following command 
  ```
  [ec2-user@ip-172-22-2-2 ~]$ pwd
  ## output will as follows 
  /home/ec2-user
  ```
  2. Click on <<Explorer>> within vscode and then click on <<Open Folder>> and pick /home/ec2-user
  3. Perform a quick update on your instance and install git
```
sudo yum update -y 
sudo yum install git -y
```
5. Restart vscode 
6. Click on <<Source>> icon and then click on <<Clone Repository>> and provide this url https://github.com/aws-samples/eks-workshop-v2.git
7.  Install home brew, ec2 will ask for sudo password just enter somhting for three times
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```
8. Run these two commands in your terminal to add Homebrew to your PATH:
    echo 'eval "$(/home/ec2-user/.linuxbrew/bin/brew shellenv)"' >> /home/ec2-user/.bash_profile
    eval "$(/home/ec2-user/.linuxbrew/bin/brew shellenv)"
- Install Homebrew's dependencies if you have sudo access:
    sudo yum groupinstall 'Development Tools'
9.  Intall dpendencies and a hugo fix  
```
brew install hugo
brew install terraform
brew install jq
git submodule update --init
```
10. Run a live local server that renders the final web site 
```
make serve
```
Note: This command does not return, if you want to stop it use Ctrl+C.
You can then access the content at `http://localhost:1313`. 

Your environment is up and running revew [Authoring Content](authoring_content.md) next steps



 