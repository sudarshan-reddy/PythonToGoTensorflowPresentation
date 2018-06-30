## Train with Python. <br> Predict with Go.  
##### <span style="font-family:Helvetica Neue; font-weight:bold"><span style="color:#e49436">Making the most of the best of both worlds</span></span>

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

![before](assets/before.png)

---

![after](assets/after.png)

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

+++
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Python + Flask</span> 
</br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">16393 requests in 30s</span>
+++
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Python + Twisted</span> 
</br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">102781 requests in 30s</span>
+++
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">PyPy2.7 Python + Twisted</span> 
</br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">379001 requests in 30s</span>
+++
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Node + Express</span> 
</br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">268866 requests in 30s</span>
+++
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Go + Chi</span> 
</br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">1550508 requests in 30s</span>

+++
@title[Request handling benchmarks]

<canvas class="stretch" data-chart="horizontalBar">
<!--
{
 "data" : {
  "labels" : ["Python + Flask", "Python + Twisted", "PyPy2.7 Python + Twisted",
    "Node + Express", "Go + Chi"],
  "datasets" : [{
    "data": [16393, 102781, 379001, 268866, 1550508],
    "backgroundColor": "#e49436",
    "borderColor": "#e49436"
  }]
  },
  "options": {
    "title": {
      "display": true,
      "text": "Request handling benchmarks",
      "fontColor": "gray",
      "fontSize": 20
    },
    "legend": {
      "display": false
    },
    "scales": {
      "xAxes": [{
        "ticks": {
            "beginAtZero": true,
            "max": 1600000,
            "stepSize": 10000,
            "fontColor": "gray"
        },
        "scaleLabel": {
          "display": true,
          "labelString": "Requests in 30s",
          "fontColor": "gray"
        }
      }],
      "yAxes": [{
        "ticks": {
            "fontColor": "gray"
        }
      }]
    }
  }
}
-->
</canvas>

---

## <span style="font-family:Rockitt; font-weight:bold">Proof of Concept</span>

---

## <span style="font-family:Rockitt; font-weight:bold">Things to take note</span>


<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Keras is a great library for beginners</span> 
</br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Tensorflow has bindings for both Go and Python</span> 
</br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Tensorflow graphs are language agnostic</span> 

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

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Copy the graph folder to a common location</span> 

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

## <span style="font-family:Rockitt; font-weight:bold">Plugs</span>

<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Blog: https://sudarshan-reddy.github.io/post/keras-to-go/ </span>
<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Training Code: https://github.com/sudarshan-reddy/resnetClassifier</span>
<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Prediction Code: https://github.com/sudarshan-reddy/resnetPredictor</span>
<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Library for Go: https://github.com/sudarshan-reddy/telemus
</span>
<br>

---

<span style="font-family:Rockitt; font-weight:bold">That's it. Really!</span>

---
