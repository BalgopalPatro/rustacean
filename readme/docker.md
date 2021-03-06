# Running Rustacean as a Docker container


## Contribute

To work on the bot with docker you must build the `Dockerfile.test` image.

First of all, set up your working directory:

Create a clone of this project on your development machine:
```sh
user@machine:~$ git clone https://github.com/Kryod/rustacean.git
```

Register a Discord bot here https://discordapp.com/developers/applications/me

Set up your environment:
```sh
user@machine:~$ cd rustacean/ # Go to your copy of this repository
user@machine:~/rustacean$ cp config.toml.example config.toml
user@machine:~/rustacean$ nano config.toml # Edit this file to set your Discord bot credentials
```

Then you can build the image:

```sh
user@machine:~/rustacean$ sudo docker build -t rusttest -f Dockerfile.test .
```

Now you have an environnement with all the required dependencies. To start working you need to run the docker and link it with the folder where you "git cloned" this repository.
You need to link your daemon docker with the container so it can spawn other containers (and you can control all the containers and view all the images available).

```sh
user@machine:~/rustacean$ sudo docker run -it -v "$(pwd)":/home -v /var/run/docker.sock:/var/run/docker.sock rusttest
root@docker-id:/home$ cargo run update-db
```

Now your changes locally will affect files on the docker and vice-versa. You can launch the bot with `cargo run` or test with `cargo test`. 

## Deploy

To deploy the bot you need to make the image. Do not forget to set your config.toml.
```sh
user@machine:~/rustacean$ sudo docker pull kryod/rustacean:latest
```

Now you want this image to run on a server or something that will be on 24/7. You will probably want to have access to the logs of the bot if it crashes so you need to link the `rustacean.log` file in this directory to the `rustacean.log` file on the docker container.
```sh
user@machine:~/rustacean$ touch rustacean.log rustacean.sqlite3
user@machine:~/rustacean$ sudo docker run --name="rustacean" -t --restart="always" -d -v "$(pwd)/rustacean.log":/home/rustacean.log -v "$(pwd)/rustacean.sqlite3":/home/rustacean.sqlite3 -v "$(pwd)/config.toml":/home/config.toml -v /var/run/docker.sock:/var/run/docker.sock kryod/rustacean:latest
user@machine:~/rustacean$ sudo docker exec -it rustacean cargo run --release update-db # Remember to always run this command if you pull updates from the repository in order to keep your database up to date
user@machine:~/rustacean$ tail -f rustacean.log # You can now run this to monitor the bot
```
