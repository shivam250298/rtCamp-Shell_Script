# rtCamp-Shell_Script

# Create a command-line script, preferably in Bash, PHP, Node, or Python to perform the following tasks:

1- Check if docker and docker-compose is installed on the system. If not present, install the missing packages.
2- The script should be able to create a WordPress site using the latest WordPress Version. Please provide a way for the user to provide the site name as a command-line argument.
3- It must be a LEMP stack running inside containers (Docker) and a docker-compose file is a must.
4-Create a /etc/hosts entry for example.com pointing to localhost. Here we are assuming the user has provided example.com as the site name.
5- Prompt the user to open example.com in a browser if all goes well and the site is up and healthy.
6-Add another subcommand to enable/disable the site (stopping/starting the containers)
7-Add one more subcommand to delete the site (deleting containers and local files).

