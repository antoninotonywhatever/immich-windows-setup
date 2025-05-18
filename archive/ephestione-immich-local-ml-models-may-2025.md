This is an archive of this post: https://www.ephestione.it/immich-manually-download-machine-learning-ai-models/

howto's, linux
Immich: manually download machine learning AI models
30 December, 2024 ephestione Leave a comment
The information on how to do this is scattered here and there, so I thought I’d make a post for self reference and to help other people as well.

This is based on Immich 1.123.

My issue was that all jobs scheduled for machine learning didn’t work, and checking the logs in portainer I noticed a great many deal of these  repeated snippets for the immich-server container:

[Nest] 6 - 12/30/2024, 10:57:49 AM WARN [Microservices:MachineLearningRepository] Machine learning request to "http://immich-machine-learn
ing:3003" failed with status 500: Internal Server Error
[Nest] 6 - 12/30/2024, 10:57:49 AM ERROR [Microservices:JobService] Unable to run job handler (smartSearch/smart-search): Error: Machine le
arning request '{"clip":{"visual":{"modelName":"ViT-B-32__openai"}}}' failed for all URLs
[Nest] 6 - 12/30/2024, 10:57:49 AM ERROR [Microservices:JobService] Error: Machine learning request '{"clip":{"visual":{"modelName":"ViT-B-
32__openai"}}}' failed for all URLs
at MachineLearningRepository.predict (/usr/src/app/dist/repositories/machine-learning.repository.js:41:15)
at process.processTicksAndRejections (node:internal/process/task_queues:105:5)
at async MachineLearningRepository.encodeImage (/usr/src/app/dist/repositories/machine-learning.repository.js:59:26)
at async SmartInfoService.handleEncodeClip (/usr/src/app/dist/services/smart-info.service.js:104:27)
at async JobService.onJobStart (/usr/src/app/dist/services/job.service.js:148:28)
at async EventRepository.onEvent (/usr/src/app/dist/repositories/event.repository.js:134:13)
at async Worker.processJob (/usr/src/app/node_modules/bullmq/dist/cjs/classes/worker.js:394:28)
at async Worker.retryIfFailed (/usr/src/app/node_modules/bullmq/dist/cjs/classes/worker.js:581:24)
[Nest] 6 - 12/30/2024, 10:57:49 AM ERROR [Microservices:JobService] Object:
{
"id": "82ce81c7-5e2f-4192-8556-dfe01098a935"
}

while this was happening in immich-machine-learning:

[12/30/24 10:53:23] INFO Downloading visual model 'ViT-B-32__openai'. This
may take a while.
[12/30/24 10:53:23] WARNING Failed to load visual model 'ViT-B-32__openai'.
Clearing cache.
[12/30/24 10:53:23] WARNING Attempted to clear cache for model
'ViT-B-32__openai', but cache directory does not
exist
[12/30/24 10:53:23] INFO Downloading visual model 'ViT-B-32__openai'. This
may take a while.
[12/30/24 10:53:23] ERROR Exception in ASGI application

together with some permission denied errors under the /cache path.

So I started searching, and what I came up with is that I had to login in the machine learning container, with

docker ps #note the immich-machine-learning id
docker exec -it -u 0 <container id> bash

to open a shell with root privileges into that container, otherwise I couldn’t write to the disk.

Then, from within the container:

mkdir -p /cache/clip
mkdir -p /cache/facial-recognition
exit

At this point, you will be back in your host system shell, and make sure you have the necessary tools installed:

sudo apt install git git-lfs

then open immich’s HuggingFace repository and browse to the models you need, you will likely have to do the following (replacing machineid with your correct value):

cd /tmp
git clone https://huggingface.co/immich-app/ViT-B-32__openai
git clone https://huggingface.co/immich-app/buffalo_l
docker ps #note immich-machine-learning id
docker cp ViT-B-32__openai machineid:/cache/clip/
docker cp buffalo_l machineid:/cache/facial-recognition/
docker exec -u 0 machineid chmod -R 777 /cache

The last command is needed because I was getting many errors like:

PermissionError: [Errno 13] Permission denied:
'/cache/facial-recognition/buffalo_l/recognition/mo
del.onnx'

At this point restart the machine learning container with

docker restart machineid

and all should be fine.
