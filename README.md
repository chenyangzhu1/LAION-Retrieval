# LAION_Retrieval

Fast retrieval on LAION subsets with [clip-retrieval](https://github.com/rom1504/clip-retrieval).

Similarity retrieval in large image-text datasets (such as [LAION](https://laion.ai/)) allows for quick and convenient expansion of existing datasets. 
This technique has been extensively used in AIGC realted methods like [Custom Diffusion](https://github.com/adobe-research/custom-diffusion).

However, due to [safety reviews](https://laion.ai/notes/laion-maintenance/), the [knn-service](https://knn.laion.ai/knn-service) provided by LAION has been discontinued.
To tackle thie problem. This repository offers a solution for image-text retrieval based on a LAION subset ([improved_aesthetics_6.5plus](https://laion.ai/blog/laion-aesthetics/)).


## ⚙️ Setup

See the complete code in the [script][note].

### Environment Configuration

You can run this [bash script][setup] or use the following command to obtain the bounding box of the image.

```shell
conda create -n LAION python=3.9
conda activate LAION
pip install -r requirements.txt
```

### Data Preparation

1. Download the [clip index](https://huggingface.co/datasets/zcaoyao/improved_aesthetics_6.5plus_clip_retrieval) of LAION subset.

2. Update the `ClipOptions` and `./indices_paths.json` according to your specific clip index path and model needs. For detailed option settings, see [clip-retrieval](https://github.com/rom1504/clip-retrieval#clip-back).


```python
indice_folder = "./improved_aesthetics_6.5plus_clip_retrieval"
clip_options = ClipOptions(
    indice_folder=indice_folder,
    clip_model="open_clip:ViT-B-32/laion2b_s34b_b79k",
    enable_hdf5=False,
    enable_faiss_memory_mapping=False,
    columns_to_return=["url", "caption"],
    reorder_metadata_by_ivf_index=False,
    enable_mclip_option=False,
    use_jit=False,
    use_arrow=False,
    provide_safety_model=False,
    provide_violence_detector=False,
    provide_aesthetic_embeddings=False,
)
```

## 🚀 Running the Retrieval

1. **Input Image**: Place the image you want to query in the path and specify its path in the script, e.g., `input_image = "example/cat.jpg"`
2. **Execute Retrieval**: Run the script to perform the image retrieval. The main parts involve:
    - Loading and encoding the input image.
    - Performing KNN search using the `KnnService`.
    - Storing the results in a predefined directory.
3. **Output**:
    - The script will save the retrieval results in a JSON file located in the `./result` directory. The file is named with a timestamp to easily track different retrieval sessions.

    - Example output:
```json
[  
    {
        "caption": "Photograph Cat&girl by Imantas Boiko on 500px",
        "id": 35124,
        "similarity": 0.74631267786026
    }
]
```

4. **Query Metadata**:
   - The id is the position of the item in the clip index. It may be used to query metadata with `ParquetMetadataProvider`
   - Example output:
```json
[
    {
        "image_path": "000051066", 
        "caption": "Photograph Cat&girl by Imantas Boiko on 500px"
    }
]
```

5. This `image path` can be used to obtain complete entry information in the source dataset.
   - Download the [source dataset](https://huggingface.co/akameswa/improved_aesthetics_6.5plus_webdataset) of LAION subset and unzip it.
   - Fill in the unzipped path into `source_root`.
   - The script will save the source dataset results in a JSON file located in the `./result` directory.

## 🙏 Acknowledgement

This repository borrows heavily from [clip-retrieval](https://github.com/rom1504/clip-retrieval).
Thanks to the authors for sharing their code and models.

[setup]: setup_env.sh
[note]: retrieval.ipynb
