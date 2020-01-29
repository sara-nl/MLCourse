# HPC & BIG DATA - UvA 2020

Repository to host contents of the High Performance Machine Learning course within HPC & BIG DATA in 2020

![alt text](https://www.google.com/imgres?imgurl=https%3A%2F%2Fuserinfo.surfsara.nl%2Fsites%2Fdefault%2Ffiles%2FCartesius_0_0.jpg&imgrefurl=https%3A%2F%2Fuserinfo.surfsara.nl%2Fsystems%2Fcartesius&docid=hwVDhUi-ydJE7M&tbnid=KnWsYkzBqExjmM%3A&vet=10ahUKEwiu_eLAjannAhUkNOwKHRlTAEgQMwhCKAAwAA..i&w=1800&h=1200&bih=740&biw=1440&q=cartesius%20surfsara&ved=0ahUKEwiu_eLAjannAhUkNOwKHRlTAEgQMwhCKAAwAA&iact=mrc&uact=8)

# Setup

Start up a JupyterHub instance by going to the link:    
```
https://jupyter2.lisa.surfsara.nl/course
```
- Login with your username sdemoXXX and password
- Select the **UvA course** from the dropdown menu
- Wait for the server to be spawned...
- Good to go!

> We are going to use this JupyterHub instance on Lisa GPU for the practicals today. 

- Okay now, locate the dropdown button that says **New** in the top right corner, and start a Terminal window
> Now you are in your /home/sdemoXXX directory within the JupyterHub environment
- Run the command below to clone this git repository in your home directory:

```
git clone https://github.com/sara-nl/MLCourse.git
```
> This will clone the repository in your /home/sdemoXXX directory
- Go to the MLCourse directory and run install.sh:
```
cd $HOME/MLCourse
sh install.sh
```
> Nice! We have installed the environment for today, so let's start a Jupyter Notebook
- Return to your **Home** tab, and start a Jupyter Notebook with the *UvA Course kernel* by selecting **UvA Course** under **New** >> Notebook
