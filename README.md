# rtCamp-Shell_Script

# Create a command-line script, preferably in Bash, PHP, Node, or Python to perform the following tasks:

1- Check if docker and docker-compose is installed on the system. If not present, install the missing packages.

2- The script should be able to create a WordPress site using the latest WordPress Version. Please provide a way for the user to provide the site name as a command-line argument.

3- It must be a LEMP stack running inside containers (Docker) and a docker-compose file is a must.

4-Create a /etc/hosts entry for example.com pointing to localhost. Here we are assuming the user has provided example.com as the site name.

5- Prompt the user to open example.com in a browser if all goes well and the site is up and healthy.

6-Add another subcommand to enable/disable the site (stopping/starting the containers)

7-Add one more subcommand to delete the site (deleting containers and local files).

#Solution =

#!/bin/bash

# Check if a command is installed, install if missing
check_dependency() {
    local command_name=$1
    local package_name=$2

    if ! command -v $command_name &> /dev/null; then
        echo "$package_name is not installed. Installing $package_name..."
        if [ -n "$(command -v apt-get)" ]; then
            apt-get update
            apt-get install -y $package_name
        elif [ -n "$(command -v apt)" ]; then
             apt update
             apt install -y $package_name
        else
            echo "Unable to install $package_name. Please install it manually."
            exit 1
        fi
        echo "$package_name installed successfully."
    fi
}

# Create a WordPress site using the latest version
create_wordpress_site() {
    if [ $# -eq 0 ]; then
        echo "Please provide the site name as a command-line argument."
        exit 1
    fi

    local site_name=$1

    echo "Creating WordPress site: $site_name"
    mkdir $site_name && cd $site_name

    cat > docker-compose.yml <<EOF
version: '3'
services:
  db:
    image: mariadb
    environment:
      MYSQL_ROOT_PASSWORD: example
      MYSQL_DATABASE: wordpress
      MYSQL_USER: wordpress
      MYSQL_PASSWORD: wordpress
  wordpress:
    depends_on:
      - db
    image: wordpress
    ports:
      - 80:80
    restart: always
    environment:
      WORDPRESS_DB_HOST: db
      WORDPRESS_DB_NAME: wordpress
      WORDPRESS_DB_USER: wordpress
      WORDPRESS_DB_PASSWORD: wordpress
EOF

    docker-compose up -d
}

# Add /etc/hosts entry
add_hosts_entry() {
    sudo sed -i '/example.com/d' /etc/hosts
    echo "127.0.0.1    example.com" | sudo tee -a /etc/hosts
}

# Prompt user to open example.com in browser
open_in_browser() {
    echo "WordPress site created successfully. Opening example.com in a browser..."
    xdg-open "http://example.com"
}

# Enable/Disable site (stop/start containers)
toggle_site() {
    local site_name=$1

    if [ "$2" == "enable" ]; then
        echo "Enabling site: $site_name"
        docker-compose start
    elif [ "$2" == "disable" ]; then
        echo "Disabling site: $site_name"
        docker-compose stop
    fi
}

# Delete the site (delete containers and local files)
delete_site() {
    local site_name=$1

    echo "Deleting site: $site_name"
    docker-compose down -v
    cd ..
    rm -rf $site_name
}

# Main script logic
main() {
    check_dependency "docker" "Docker"
    check_dependency "docker-compose" "Docker Compose"
    create_wordpress_site $1
    add_hosts_entry
    open_in_browser

    if [ "$2" == "enable" ] || [ "$2" == "disable" ]; then
        toggle_site $1 $2
    elif [ "$2" == "delete" ]; then
        delete_site $1
    fi
}

# Run the script with command-line arguments
main "$@"
