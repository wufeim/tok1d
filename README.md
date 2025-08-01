# tok1d

**Python Packaging for [1D Visual Tokenization and Generation](https://github.com/bytedance/1d-tokenizer/).**

## Installation

**On local machine:**

```sh
conda create -n tok1d python=3.10
pip install torch==2.3.1 torchvision==0.18.1 torchaudio==2.3.1 --index-url https://download.pytorch.org/whl/cu121
pip install git+https://github.com/wufeim/tok1d.git
```

**On Colab:**

```sh
pip install git+https://github.com/wufeim/tok1d.git
```

## Examlpe Usage

```py
from io import BytesIO
import requests

import numpy as np
from PIL import Image
import torch

from tok1d.modeling.titok import TiTok
from tok1d.utils import example_images

titok_tokenizer = TiTok.from_pretrained(
    'yucornetto/tokenizer_titok_l32_imagenet')
titok_tokenizer.eval()
titok_tokenizer.requires_grad_(False)
titok_tokenizer.to('cuda')

url = example_images[1]
response = requests.get(url)
image = Image.open(BytesIO(response.content))

image_tensor = torch.from_numpy(
    np.array(image).astype(np.float32)
).permute(2, 0, 1).unsqueeze(0) / 255.0

encoded_tokens = titok_tokenizer.encode(
    image_tensor.to('cuda'))[1]['min_encoding_indices']

def decode(tokens):
    recon = titok_tokenizer.decode_tokens(tokens)
    recon = torch.clamp(recon, 0.0, 1.0)
    recon = (recon * 255.0).permute(0, 2, 3, 1).to(
        'cpu', dtype=torch.uint8).numpy()[0]
    return Image.fromarray(recon)

decoded_image = decode(encoded_tokens)
```

## Citing

Please use the following BibTeX entries from the original **[1d-tokenizers](https://github.com/bytedance/1d-tokenizer/).**

```BibTeX
@article{he2025flowtok,
  author    = {Ju He and Qihang Yu and Qihao Liu and Liang-Chieh Chen},
  title     = {FlowTok: Flowing Seamlessly Across Text and Image Tokens},
  journal   = {arXiv preprint arXiv:2503.10772},
  year      = {2025}
}
```

```BibTeX
@article{kim2025democratizing,
  author    = {Dongwon Kim and Ju He and Qihang Yu and Chenglin Yang and Xiaohui Shen and Suha Kwak and Liang-Chieh Chen},
  title     = {Democratizing Text-to-Image Masked Generative Models with Compact Text-Aware One-Dimensional Tokens},
  journal   = {arXiv preprint arXiv:2501.07730},
  year      = {2025}
}
```

```BibTeX
@article{yu2024randomized,
  author    = {Qihang Yu and Ju He and Xueqing Deng and Xiaohui Shen and Liang-Chieh Chen},
  title     = {Randomized Autoregressive Visual Generation},
  journal   = {arXiv preprint arXiv:2411.00776},
  year      = {2024}
}
```

```BibTeX
@article{yu2024an,
  author    = {Qihang Yu and Mark Weber and Xueqing Deng and Xiaohui Shen and Daniel Cremers and Liang-Chieh Chen},
  title     = {An Image is Worth 32 Tokens for Reconstruction and Generation},
  journal   = {NeurIPS},
  year      = {2024}
}
```
