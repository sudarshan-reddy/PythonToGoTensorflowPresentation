## Train with Python. Predict with Go. 
##### <span style="font-family:Helvetica Neue; font-weight:bold"><span style="color:#e49436">Because we can have cake and eat it too</span></span>

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

## <span style="font-family:Rockitt; font-weight:bold">The plan</span>

+++

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Keras is a great library for beginners</span> 

+++ 

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Tensorflow has bindings for both Go and Python</span> 

+++

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Tensorflow graphs are language agnostic</span> 

+++


---


## <span style="font-family:Rockitt; font-weight:bold">Training</span>


---

```python
    sess = tf.Session()
    K.set_session(sess)

    train_gen, val_gen = load_celeb_data(224, 224)
    model = keras.applications.resnet50.ResNet50()
    classes = train_gen.class_indices
    
    model.layers.pop()

    for layer in model.layers:
        layer.trainable = False
    for layer in model.layers[:30]:
        layer.trainable = True
    last = model.layers[-1].output

    x = Dense(len(classes), activation="softmax", name="inferenceLayer")(last)

    finetuned_model = Model(model.input, x)

    finetuned_model.compile(optimizer=Adam(lr=1e-04), loss='categorical_crossentropy', metrics=['accuracy'])

    for c in list(train_gen.class_indices):
        classes[train_gen.class_indices[c]] = c
    finetuned_model.classes = classes

    for n in tf.get_default_graph().as_graph_def().node:
        print(n.name)

    early_stopping = EarlyStopping(patience=10)
    checkpointer = ModelCheckpoint('resnet50_best.h5', verbose=1, save_best_only=True)

    finetuned_model.fit_generator(train_gen, 
                                  steps_per_epoch=100, epochs=1,
                                  callbacks=[early_stopping, checkpointer],
                                  validation_data=val_gen,
                                  validation_steps=100
                                 )
    finetuned_model.save('resnet50_final.h5')

    builder = tf.saved_model.builder.SavedModelBuilder("forGo")
    builder.add_meta_graph_and_variables(sess, ["tags"])
    builder.save()
    sess.close()
```


---
