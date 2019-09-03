# Tensorflow Leaf Detect

1. git clone https://github.com/sanjeeviisl/tensorflow_leaf_detect.git

2. cd tensorflow_leaf_detect

3. Set the parameter for  Model

   IMAGE_SIZE=224
   ARCHITECTURE="mobilenet_1.0_${IMAGE_SIZE}"


4. command to retrain the Model for Leaf Detect

   python -m scripts.retrain \
    --bottleneck_dir=leaf_tf_files/bottlenecks \
    --how_many_training_steps=500 \
    --model_dir=leaf_tf_files/models/ \
    --summaries_dir=leaf_tf_files/training_summaries/"${ARCHITECTURE}" \
    --output_graph=leaf_tf_files/retrained_graph.pb \
    --output_labels=leaf_tf_files/retrained_labels.txt \
    --architecture="${ARCHITECTURE}" \
    --image_dir=leaf_tf_files/leaf_photos

5. Test a Leaf Lebel to validate the Model

    python -m scripts.label_image \
        --graph=leaf_tf_files/retrained_graph.pb  \
	--labels=leaf_tf_files/retrained_labels.txt \
	--image=leaf_tf_files/leaf_photos/Castaneasativa/Castaneasativa-51.jpg 


# TensorFlow for Poets 2: TFLite Android

1. pip install PILLOW

2. check for TOCO Installation

3. IMAGE_SIZE=224

4. toco \
    --input_file=leaf_tf_files/retrained_graph.pb \
    --output_file=leaf_tf_files/optimized_graph.lite \
    --input_format=TENSORFLOW_GRAPHDEF \
    --output_format=TFLITE \
    --input_shape=1,${IMAGE_SIZE},${IMAGE_SIZE},3 \
    --input_array=input \
    --output_array=final_result \
    --inference_type=FLOAT \
    --input_data_type=FLOAT


# Tensorflow Leaf Detect V2 Version

    // Train a Leaf Label
    python -m scripts.retrain  \
      --bottleneck_dir=leaf_tf_files_v2/bottlenecks \
      --how_many_training_steps=5000 \
      --model_dir=leaf_tf_files_v2/models/ \
      --summaries_dir=leaf_tf_files_v2/training_summaries/"${ARCHITECTURE}" \
      --output_graph=leaf_tf_files_v2/retrained_graph.pb \
      --output_labels=leaf_tf_files_v2/retrained_labels.txt \
      --architecture="${ARCHITECTURE}" \
      --image_dir=leaf_tf_files_v2/leaf_photos


    // Test a Leaf Label in Image
    1. python -m scripts.label_image \
      --graph=leaf_tf_files_v2/retrained_graph.pb \
      --labels=leaf_tf_files_v2/retrained_labels.txt \
      --image=leaf_tf_files_v2/leaf_photos/magnolia_stellata/13001968947957.jpg

    2. pip install PILLOW

    3. toco \
      --graph_def_file=leaf_tf_files_v2/retrained_graph.pb \
      --output_file=leaf_tf_files_v2/optimized_graph.lite \
      --input_format=TENSORFLOW_GRAPHDEF \
      --output_format=TFLITE \
      --input_shape=1,${IMAGE_SIZE},${IMAGE_SIZE},3 \
      --input_array=input \
      --output_array=final_result \
      --inference_type=FLOAT \
      --input_data_type=FLOAT


# Tensorflow Leaf Detect V3 Version

1. IMAGE_SIZE=224 

2. ARCHITECTURE="mobilenet_0.50_${IMAGE_SIZE}"

3. ARCHITECTURE="mobilenet_1.0_${IMAGE_SIZE}"

4. python -m scripts.retrain  \
     --bottleneck_dir=leaf_tf_files_v3/bottlenecks \
     --how_many_training_steps=5000 \
     --model_dir=leaf_tf_files_v3/models/ \
     --summaries_dir=leaf_tf_files_v3/training_summaries/"${ARCHITECTURE}" \
     --output_graph=leaf_tf_files_v3/retrained_graph.pb \
     --output_labels=leaf_tf_files_v3/retrained_labels.txt \
     --architecture="${ARCHITECTURE}" \
     --image_dir=leaf_tf_files_v3/leaf_photos


5. Test a Label in Image
    python -m scripts.label_image \
      --graph=leaf_tf_files_v3/retrained_graph.pb \
      --labels=leaf_tf_files_v3/retrained_labels.txt \
      --image=leaf_tf_files_v3/leaf_photos/magnolia_stellata/13001968947957.jpg


# FOR TF MOBILE

1. python -m tensorflow.python.tools.optimize_for_inference \
  --input=leaf_tf_files_v3/retrained_graph.pb \
  --output=leaf_tf_files_v3/optimized_graph.pb \
  --input_names="input" \
  --output_names="final_result"
  

2. python -m scripts.quantize_graph \
  --input=leaf_tf_files_v3/optimized_graph.pb \
  --output=leaf_tf_files_v3/rounded_graph.pb \
  --output_node_names=final_result \
  --mode=weights_rounded
--graph_def_file=leaf_tf_files_v3/retrained_graph.pb \

3. toco \
--output_file=leaf_tf_files_v3/optimized_graph.lite \
--input_format=TENSORFLOW_GRAPHDEF \
--output_format=TFLITE \
--input_shape=1,${IMAGE_SIZE},${IMAGE_SIZE},3 \
--input_array=input \
--output_array=final_result \
--inference_type=FLOAT \
--input_data_type=FLOAT

# TF LITE Mobile

1. Test a Label in Image

python -m scripts.label_image \
--graph=leaf_tf_files_v3/rounded_graph.pb \
--labels=leaf_tf_files_v3/retrained_labels.txt \
--image=leaf_tf_files_v3/leaf_photos//221._ulmus_parvifolia/13291720722746_crop3_flip_3.jpg

python -m scripts.label_image \
--graph=leaf_tf_files_v3/optimized_graph.pb \
--labels=leaf_tf_files_v3/retrained_labels.txt \
--image=leaf_tf_files_v3/leaf_photos//221._ulmus_parvifolia/13291720722746_crop3_flip_3.jpg

~/tensorflow/bazel-bin/tensorflow/tools/graph_transforms/transform_graph \
  --in_graph=leaf_tf_files_v3/optimized_graph.pb \
  --out_graph=leaf_tf_files_v3/final_graph.pb \
  --inputs=input \
  --outputs=final_result \
  --transforms="remove_nodes(op=PlaceholderWithDefault) strip_unused_nodes(type=float, shape=\"1,299,299,3\") sort_by_execution_order"
  
  
