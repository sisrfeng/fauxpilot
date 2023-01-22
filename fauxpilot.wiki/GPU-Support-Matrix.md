This page documents the GPU configurations that are known to work (or not work) and what models they are known to work with. If you have used FauxPilot with a GPU and model configuration that's not listed in the table below, please add it!

| GPU Model | VRAM | Number of GPUs | Working? | Models | Notes | Checker |
| --------- | ---- | -------------- | -------- | ------ | ----- | ------- |
| NVIDIA Tesla A100 | 80GB | 1 | Yes | 350M, 2B, 6B, 16B | | @leemgs |
| NVIDIA RTX A6000 | 48GB | 1, 2, 4 | Yes | 350M, 2B, 6B, 16B | | @moyix |
| NVIDIA RTX A4000 | 16GB | 1 | Yes | 6B | | @grantharris33 |
| NVIDIA RTX 3090 | 24GB | 1 | Yes | 2B | | @152334H |
| NVIDIA RTX 3090 | 24GB | 1 | Yes | 6B |  Docker-in-WSL2 & fauxpilot-windows | @Frederisk |
| NVIDIA RTX 3080Ti| 12GB | 1 | Yes | 350M, 2B | | @??? |
| NVIDIA RTX 3060Ti | 8GB | 1| Yes | 350M, 2B | Tested in Docker-in-WSL | @dewacandra4 |
| NVIDIA RTX 2080Ti| 12GB | 1 | Yes | 350M, 2B, 6B | | @leemgs |
| NVIDIA RTX 2060 XC | 12GB | 1 | Yes | 2B | Tested in Docker-in-WSL | @azeemba |
| NVIDIA GTX 1080Ti | 11GB | 1 | Yes | 350M, 2B | | @??? |
| NVIDIA GTX 1060 | 6GB | 1 | Yes | 350M | Docker-in-WSL2 & fauxpilot-windows | @Frederisk |
| NVIDIA Titan Xp | 12GB | 1 | Yes | 350M, 2B, 6B | | @leemgs |
