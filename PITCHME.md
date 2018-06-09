## Train with Python. <br> Predict with Go.  
##### <span style="font-family:Helvetica Neue; font-weight:bold"><span style="color:#e49436">Making the most of the best of both worlds</span></span>

---

## <span style="font-family:Rockitt; font-weight:bold">Deep Learning is popular</span>
Note: Give a two minute crash course. Feedforward / Feedback. Train and Predict.

---

## <span style="font-family:Rockitt; font-weight:bold"> Why is Python good?</span>

<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Pseudo code like syntax</span> 
<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Doesn't need too much parallelism (GPU)</span> 
<br>
<span class="fragment" data-fragment-index="2" style="font-family:Hattori Hanzo;">Libraries</span> 
<br>
<span class="fragment" data-fragment-index="4" style="font-family:Hattori Hanzo;">Community</span>  

---

## <span style="font-family:Rockitt; font-weight:bold">Twin Discplines</span>

<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Training</span> 
<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Prediction</span> 
<br>

--- 

## <span style="font-family:Rockitt; font-weight:bold">Divide and Conquer</span>

<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Train with Python</span> 
<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Predict with Go</span> 
<br>


---

![OverEngineering](https://jrlburke.files.wordpress.com/2015/10/could-should.jpg)

---

## <span style="font-family:Rockitt; font-weight:bold">Why Go is good?</span>

<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Light weight</span> 
<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Compiled Binary makes deployment a breeze</span> 
<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Faster if you want to put your predictions into a webserver</span> 
<br>

---

## <span style="font-family:Rockitt; font-weight:bold">Request handling Benchmarks</span>
```
    1a. Go/Chi:
    51684 Requests/sec => 1550508 requests in 30s
    2. Sparkjava:
    48631 Requests/sec => 1458768 requests in 30s
    3a. PyPy2.7 Python + Twisted:
    12633 Requests/sec => 379001 requests in 30s
    3b. Python + Twisted:
    3425 Requests/sec => 102781 requests in 30s
    3c. Python + Flask:
    11751 Requests/sec => 16393 requests in 30s 
    4. Node + Express: 
    8962 Requests/sec => 268866 requests in 30s
```

---


## <span style="font-family:Rockitt; font-weight:bold">Proof of Concept</span>

---

## <span style="font-family:Rockitt; font-weight:bold">Things to take note</span>

+++

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Keras is a great library for beginners</span> 

+++ 

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Tensorflow has bindings for both Go and Python</span> 

+++

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Tensorflow graphs are language agnostic</span> 

+++


---


## <span style="font-family:Rockitt; font-weight:bold">Training</span>

+++

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Tensorflow for Go expects three extra parameters from a traditional Keras TF model</span> 

+++

<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Tag Name</span> 
<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Input layer name</span> 
<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Inference layer name</span> 
<br>

+++

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Setting the Tensorflow session to Keras enables us to use a tag when saving the graph</span> 

```python

    import tensorflow as tf
    from keras import backend as K
    sess = tf.Session()
    K.set_session(sess)
```
+++

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">My model is a finetune of pretrained ResNet50 weights. The input layer is `input_1`</span> 
    
+++

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Name the inference layer</span> 
```python
    x = Dense(len(classes), activation="softmax", name="inferenceLayer")(last)
```

+++


<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">After training, save the model with a tag</span> 

```python
    builder = tf.saved_model.builder.SavedModelBuilder("forGo")
    builder.add_meta_graph_and_variables(sess, ["tags"])
    builder.save()
    sess.close()
```

+++

---

## <span style="font-family:Rockitt; font-weight:bold">Prediction</span>

+++

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Copy the graph file (extension .pb) to a common file location</span> 

+++

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Load the saved model that we trained with Keras and Tensorflow</span>

```go
    model, err := tf.LoadSavedModel("forGo", []string{"tags"}, nil)
	if err != nil {
		log.Fatal(err)
	}
```

+++

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">And run the call `Exec` on the model to make the prediction</span>

```go
    result, err := model.Session.Run(
		map[tf.Output]*tf.Tensor{
			model.Graph.Operation("input_1").Output(0): img,
		},
		[]tf.Output{
			model.Graph.Operation("inferenceLayer/Softmax").Output(0),
		},
		nil,
	)
```

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Note the explicit declaration of entry and exit layers</span>

+++

---

<span style="font-family:Rockitt; font-weight:bold">That's it. Really!</span>

---
