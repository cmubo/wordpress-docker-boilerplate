Alot of these instructions are for local only, especially the windows sections and anything to do with permissions, these instructions are insecure but are fine in a local environment.

# Getting started
IMPORTANT! On windows this should be used inside the wsl file system, not directly inside windows file system. This is due to wsl2 running through docker being extremely slow. So we bypass wsl's need to connect to the windows file system by running through the wsl filesystem itself.

This guide is a lot more complicated for windows and you should follow the windows guide futher down first, then come back to this once finished.

1. Rename env.sample to .env and change your confuration details
2. Run docker-compose up -d
3. To fix permissions, from the terminal run: `docker exec -it CONTAINER_ID chown -R www-data:www-data /var/www/html/wp-content/uploads`. Replace the CONTAINER_ID with the containers id which you can find by running `docker ps`. IMPORTANT! this will need to be ran again if the volume is delete.


# WINDOWS

## Enabling WSL
1. Go to "turn windows features on and off", you can find this via the windows search. 
2. Enable "Windows subsystem for linux"
3. Open a new terminal
4. Run `wsl --install` This will install linux, you can specify a different distribution, use `wsl --help` for more information
5. You should now be able to open a new ubuntu shell inside terminal by clicking the arrow next to the new tab button. 

## VS Code
1. Install the "WSL" Extension 
2. after that is done, in the bottom left corner, there should be a blue button, you can connect remotely to ubuntu through that. 

## Enabling Ubuntu in docker
1. go to the docker desktop settings. Then go to `resources -> WSL integration` and enable the ubuntu distribution or whatever distributions you are using.


## Running your project from WSL
1. Open a ubuntu terminal
2. Create a directory for your project
3. clone this repo into that directory (you may need to create an ssh key using ssh-keygen inside your ubuntu environment).
4. now run the getting started steps at the top of the README
5. You will likely need to run the steps at the "FILE PERMISSIONS" section in the notes.



# Notes

- IMAGES: Image uploads wont be reflected on the filesystem, and wont be uploaded either. Any images will need to be manually copied over to the docker container using a `docker cp` command. You can copy the whole uploads directory by running this command:
```
docker cp ./wordpress/wp-content/uploads CONTAINER_ID:/var/www/html/wp-content/
```


- HTACCESS: You might need to create a .htaccess file, you can either add this into the volumes or copy one into the volume using docker copy commands. There is a basic .htaccess.sample in this root directory.
```
docker cp ./.htaccess CONTAINER_ID:/var/www/html/
```

- FILE PERMISSIONS: When running in linux, you may need to change the permissions of wp-content folder in docker or any directories you want to make changes to by running these commands:

This command adds write permissions to all files within the wp-content directory: `docker exec -it CONTAINER_ID chmod -R g+w /var/www/html/wp-content`
This command adds your current user to the www-data group `sudo usermod -a -G www-data $USER`

This should be fine since github doesnt recognise changed permissions unless using specific git config (filemode=true [Link](https://stackoverflow.com/questions/10516201/updating-and-committing-only-a-files-permissions-using-git-version-control#:~:text=By%20default%2C%20git%20will%20update,or%20track%20any%20other%20permissions.))