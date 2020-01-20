# Python Developer Environment Project

## Creating Development environment

To create the development environment for the python application, the first step I took was to create a Virtual Machine using Vagrant
which I then provisioned manually after looking at what the requirements were. To allow the application to work, I had to run the 
following shell commands:
•	sudo apt-get update -y
•	sudo apt-get upgrade -y
•	sudo apt-get install python3-pip -y
•	pip3 install -r home/vagrant/app/requirements.txt
•	mkdir /home/vagrant/Downloads
The commands above when run will update and upgrade the package lists, then go on to install the libraries required and finally will 
create the Downloads folder which his required for the app to generate the ItJobsWatchTop30.csv successfully.

## Provisioning with Cookbooks (CHEF)

For the next step, I had to automate the provision shell script which was carried out to provision the 
Ubuntu/Xenail64 machine above but this time, using Chef and Cookbooks. To move forward with this task, I had to first create a folder 
for the cookbook into which I CD into using GitBash. Once I was in this directory, I ran the “chef generate cookbook It_Jobs” command
which then created all of the default files which was required for the cookbook. The recipes within the cookbooks were used to write 
provision scripts into. The cookbook also contains two tests files which carries out unit (chef exec rspec) and integration testing 
(kitchen verify). A berksfile was added so that the cookbooks can be pulled directly from the github repository as I didn’t want the 
developers to have the cookbook file because if they accidently changed any settings, the production environment would be different and 
this would cause errors. A kitchen.yml file was also required for this step. When testing the cookbooks, I found that my 
“requirements.txt” was not being found. Fixing this ended up taking quite a bit of my time. After going through a range of fixes which
included moving around the requirement.txt file, changing the paths around and so on, the solution that was found was just to manually 
put the requirements.txt into the cookbook, runs tests, which came back as a success; after which the requirement.txt file was removed.

After running all the tests, I ran the “vagrant up” command which auto-provisioned the VM in which I then tried running the app, which 
it did. This meant that the VM was provisioned correctly and that I could move onto the next stage. 

## Jenkins (Pipelines)

The next step of the project was to use Jenkin as a slave to listen to any changes that were commited into the developer branch on 
github (app). This was done by using webhooks. If any commits were pushed onto the developer branch on github, Jenkins then runs all 
the  on the slave node to ensure that the application runs correctly without any errors and if this was the case, it would then merge 
the developer branch to the master branch. Once the merge master, packer kicks in and automatically creates up a AMI
(Amazon machine image) which then gets sent to AWS. This was the pipeline for the application.

The other pipeline was used for the cookbook. This pipeline works similar to the previous one in the sense that when you push a change
into the developer branch, it runs the chef exec rspec (unit tests) and the kitchen verify (intergration testing) to ensure that there 
are no mistakes and that the coobook will successfully provision correctly. If these tests were to pass, it would then merge the 
developer branch with the master branch. After this, whenever a new AMI would be created and tested, it would use the berks vendor 
to take the cookbooks from the cookbooks github repository which would be the latest version of it.
