This is part of a lab I created with DeepLearning.AI's course on MLOps. I deploy a simple pickle'd wine classification model with FastAPI and Docker.

## Building a new version of the image

The Dockerfile for this new version of the server is identical to the previous one. The only changes were done within the `main.py` file so the Dockerfile remains the same.

Now, while on the `with-batch` directory run the docker build command:

```bash
docker build -t mlepc4w2-ugl:with-batch . 
```

Since the initial layers are identical to the ones of the previous image, the build process should be fairly quick. This is another great feature of Docker called `layer caching`, which caches layers for newer builds to yield lower build times.

Notice that the image name stays the same, but the tag changed to specify that this version of the server supports batching.

## Cleaning things up

To check out the two images you have created you can use the following command:

```bash
docker images
```

Sometimes when checking all of your available images you will stumble upon some images with names and tags that have the value of `none`. These are intermediate images and if you see them listed when using the `docker images` command it is usually a good practice to prune them. 

Another way to check if you have these images in your system is to run this command:

```bash
$(docker images --filter "dangling=true" -q --no-trunc)
```

If there is no output it means there where no such images. In case there were some you can prune them by running the following command:

```bash
docker rmi $(docker images --filter "dangling=true" -q --no-trunc)
```

Now if you run again the `docker images` command you should not see those intermediate images. Things are much cleaner now!


## Running the server

Now you can run a container out of the image using the following command:

```bash
docker run --rm -p 81:80 mlepc4w2-ugl:with-batch 
```

Notice that this time, port 80 within the container maps to port 81 in your local host. This is because you probably haven't stopped the server from part 1 of this lab which is still running on port 80. It also serves to showcase how port mapping can be use to map from any port in the container to any port in the host.

Now head over to [localhost:81](http://localhost:81) and you should see a message about the server spinning up correctly.



## Make requests to the server

Once again it is time to test your server by actually using it for prediction. In the same manner as before there are some examples of batches of data within the `wine-examples` directory. 

To get the predictions for a batch of 32 wines (found in the batch_1.json file) you can send a `POST` request to the server using `curl` like this:

```bash
curl -X POST http://localhost:81/predict \
    -d @./wine-examples/batch_1.json \
    -H "Content-Type: application/json"
```

Now you should see a list with the 32 predictions (in order) for each one of the data points within the batch. **Nice work!**

## Stopping the servers

To step to servers and the containers they are running in, simply use the key combination `ctrl + c` in the terminal window where you started the process.

Alternatively you can use the `docker ps` command to check the name of the running containers and use the `docker stop name_of_container` command to stop them. Remember that you used the `--rm` flag so once you stop these containers they will also be deleted.

Now you should have a better understanding of how web servers can be used to host your machine learning models. You saw how you can use a library such as FastAPI to code the server and use Docker to ship your server along with your model in an easy manner. You also learned about some key concepts of Docker such as `image tagging` and `port mapping` and how  to allow for batching in the requests. In general you should have a clearer idea of how all these technologies interact to host models in production.

**Keep it up!**
