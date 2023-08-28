# Install Docker on Ubuntu with NVIDIA Toolkit support

I have added my verified gpg keys here. Feel free to download and verify the keys yourself :)

Install NVIDIA and CUDA drivers
===

```
sudo apt install nvidia-driver-530 nvidia-compute-utils-530
sudo reboot
```

Check that everything works:

```
nvidia-smi
```

Install Docker
===

1. Install GPG key

```
pub   rsa4096 2017-02-22 [SCEA]
      9DC8 5822 9FC7 DD38 854A  E2D8 8D81 803C 0EBF CD88
uid                      Docker Release (CE deb) <docker@docker.com>
sub   rsa4096 2017-02-22 [S]
```

```
echo -en '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
YT90qFF93M3v01BbxP+EIY2/9tiIPbrd' | base64 -d | sudo tee /etc/apt/trusted.gpg.d/docker.gpg > /dev/null
```

2. Install prerequisites

```
sudo apt install ca-certificates curl gnupg
```

3. Install Docker

```
echo \
  "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/trusted.gpg.d/docker.gpg] https://download.docker.com/linux/ubuntu \
  "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

```
sudo apt update && sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

4. Add BuiltKit config



```
echo \
'{
  "features": {
    "buildkit": true
  }
}' | sudo tee /etc/docker/daemon.json > /dev/null
```

Install NVIDIA Container Toolkit
===

1. Install gpg key:

```
pub   rsa4096 2017-09-28 [SCE]
      C95B 321B 61E8 8C18 09C4  F759 DDCA E044 F796 ECB0
uid                      NVIDIA CORPORATION (Open Source Projects) <cudatools@nvidia.com>
sub   rsa2048 2019-09-18 [S] [expired: 2021-06-16]
```

```
echo -en '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
3IsdbYd9nzmbBW7/n0InVRDrIg==' | base64 -d | sudo tee /etc/apt/trusted.gpg.d/nvidia-container-toolkit-keyring.gpg > /dev/null
```

2. Install Toolkit

Prerequisites:

```
sudo apt install curl
```

For supported distributions:

```
distribution=$(. /etc/os-release;echo $ID$VERSION_ID) && curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
sed 's#deb https://#deb [signed-by=/etc/apt/trusted.gpg.d/nvidia-container-toolkit-keyring.gpg] https://#g' | \
sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

For Ubuntu >22.04 (works great for me, but not officially supported):

You can check that the latest version defaults back to version 18.04: https://nvidia.github.io/libnvidia-container/ubuntu22.04/libnvidia-container.list

```
distribution="ubuntu18.04" && curl -s -L https://nvidia.github.io/libnvidia-container/$distribution/libnvidia-container.list | \
sed 's#deb https://#deb [signed-by=/etc/apt/trusted.gpg.d/nvidia-container-toolkit-keyring.gpg] https://#g' | \
sudo tee /etc/apt/sources.list.d/nvidia-container-toolkit.list
```

```
apt update && apt install nvidia-container-toolkit
```

3. Enable Nvidia runtime

```
sudo nvidia-ctk runtime configure
```

4. Run Test

```
sudo docker run --rm --runtime=nvidia --gpus all nvidia/cuda:11.6.2-base-ubuntu20.04 nvidia-smi
```