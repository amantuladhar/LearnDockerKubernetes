---
category: Hands On With Docker Command 
---
# Removing Containers / Images


## Deleting Container
* Container are normally ment to be object that we can easily delete.

### Delete all stopped container
#### `docker container prune`
* To delete all stopped container, we can use above command.
* Note: it will not delete the container that is running at the moment.

### Deleting single container
#### `docker container rm @id/@name`
* To delete the container we use `docker container rm @id/@name`
* Or `docker rm @id/@name`
* If you were following the tutorial you might have one container still running in background. Let's stop and delete this container.

## Deleting Image

### `docker image rm @id`
* Or `docker rmi @id`
* To delete an image we can run `docker image rm @id`.

### `docker image prune`
* This will delete all dangling images.
* Dangling image = Images that doesn't have any containers.
 



