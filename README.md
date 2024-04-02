# Practical Tutorial for Creating R with Docker
Created by: Yazdan Asgari<br>
Creation date: 2 Apr 2024<br>
Update: Apr 2024<br>
https://cesp.inserm.fr/en/equipe/exposome-and-heredity
<br>
<br>
**IMPORTANT NOTE:** This tutorial has been tested on **CentOS 7**.

In this tutorial, we want to create a Docker image including the following features
* Having an R version 3.6.3 with four installed packages (**"readr"**, **"dplyr"**, **"ggplot2"**, and **"forcats"**)
* Creating three folders (**"01_data"**, **"02_code"**, and **"03_output"**) in the Docker image.
* Copying two files (*"install_packages.R"* and *"myScript.R"*) in the **"02_code"** folder.
* Copying an input file (*"us-500.csv"*) in the **"01_data"** folder.
* Installing a text editor (called **"Vim"**) in the Docker image which enables a user to modify the RScript in the container.

  
**Steps:**
* Create a folder called **"test_docker_1"** in your Host Mchine.
* In the **"test_docker_1"** folder, Create three folders (**"01_data"**, **"02_code"**, and **"03_output"**)
* Put two files (*"install_packages.R"* and *"myScript.R"*) in the **"02_code"** folder.
* Put an input file (*"us-500.csv"*) in the **"01_data"** folder.
* In the **"test_docker_1"** folder, Create a file called "dockerfile" and write the following lines in it:
```
# Using the R software image (version 3.6.3)
FROM rocker/r-ver:3.6.3

# Creation of different folders in the Image
RUN mkdir -p /01_data
RUN mkdir -p /02_code
RUN mkdir -p /03_output


# Copying files to the created folders in the Image
COPY /02_code/install_packages.R /02_code/install_packages.R
COPY /02_code/myScript.R /02_code/myScript.R
COPY /01_data/us-500.csv /01_data/us-500.csv

# Installing four packages in the R using the "install_packages.R" file
RUN Rscript /02_code/install_packages.R

# Installing "Vim" on CentOS
RUN apt-get update && apt-get install -y vim
```
* Now it is time to build the Docker image. In the terminal, go to the **PATH** for **"test_docker_1"** folder using **cd** command
~~~
cd PATH_IN_YOUR_HOST_MACHINE/test_docker_1
~~~
* Check that you could be able to see **dockerfile** in this **PATH**:
~~~
ls
~~~
* Run the following command for building the Docker image with an arbitrary name (here we used **"docker_1"** for the name):
~~~
sudo docker build -t docker_1 .
~~~
* If all goes well without any error, you will be able to see the created Docker image (**"docker_1"**) with this command:
~~~
sudo docker images
~~~
* Now you could run the Docker image. We use the following command which contains some options:
  * Linking an existing folder (**"0_docker"**) in your Host Machine (here **"PATH_IN_YOUR_HOST_MACHINE/0_docker"**) to the **"03_output"** folder in the Docker container (**"/03_output"**) using **-v** option
~~~
-v PATH_IN_YOUR_HOST_MACHINE/0_docker:/03_output
   <------------host---------------->:<-container->
~~~
* So, run the following command to apply all the above options:
~~~
sudo docker run -it -v PATH_IN_YOUR_HOST_MACHINE/0_docker:/03_output docker_1
~~~
* If it runs without any error, you will be redirected into the R environment.
**Working inside the Docker Container:**
* To check the current path of the R environment:
```r
getwd()
```
* To check the input file in the **"01_data"** folder:
```r
list.files(path = "/01_data/")
```
* To check the files in the **"02_code"** folder:
```r
list.files(path = "/02_code/")
```
* To run the RScript:
```r
source("02_code/myScript.R")
```
After running, you will be able to see two created files (*"plot_data.csv"*, and *"myplot.png"*) in the **"PATH_IN_YOUR_HOST_MACHINE/0_docker"** in your Host Machine.
<br>
<br>
**IMPORTANT NOTE:** If you use the **"-v"** option to map a folder in the Docker container into a local folder in your Host Machine, it will erase if there is any file in the Docker container folder. For example, if you use the following command, the *"us-500.csv"* will be removed and you will get the error while running the *"myScript.R"* file:
~~~
sudo docker run -it -v PATH_IN_YOUR_HOST_MACHINE/0_docker:/03_output -v PATH_IN_YOUR_HOST_MACHINE/0_docker:/01_data docker_1
~~~
* And finally, to quit the R environment in the Docker container:
```r
q()
```
