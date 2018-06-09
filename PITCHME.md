## Train with Python. Predict with Go. 
##### <span style="font-family:Helvetica Neue; font-weight:bold"><span style="color:#e49436">Because we can have cake and eat it too</span></span>

---

## <span style="font-family:Rockitt; font-weight:bold">Deep Learning is popular</span>
Note: Give a two minute crash course. Feedforward / Feedback. Train and Predict.

---

## <span style="font-family:Rockitt; font-weight:bold"> Why Python is good?</span>

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Pseudo code like syntax</span> 
<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Doesn't need too much parallelism (GPU)</span> 
<br>
<span class="fragment" data-fragment-index="2" style="font-family:Hattori Hanzo;">Libraries</span> 
<br>
<span class="fragment" data-fragment-index="4" style="font-family:Hattori Hanzo;">Community</span>  

---

## <span style="font-family:Rockitt; font-weight:bold">Twin Discplines</span>

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

<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Light weight</span> 
<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Compiled Binary makes deployment a breeze</span> 
<br>
<span class="fragment" data-fragment-index="1" style="font-family:Hattori Hanzo;">Faster if you want to put your predictions into a webserver</span> 
<br>

---

## <span style="font-family:Rockitt; font-weight:bold">Benchmarks</span>
```
    1a. Golang + bmizerany Pat + GOMAXPROCS(7):
    51684 Requests/sec => 1550508 requests in 30s
    1b. Golang + bmizerany Pat:
    42222 Requests/sec => 1266661 requests in 30s 
    Average Latency 1.52ms
    1c. Golang + Gorilla Pat (using Gorillas Muxer)
    37756 Requests/sec => 1132689 requests in 30s 
    Average Latency 1.71ms
    2. Sparkjava:
    48631.24 Requests/sec => 1458768 requests in 30s
    Average Latency 1.29ms
    3a. PyPy2.7 Python + Twisted:
    12633 Requests/sec => 379001 requests in 30s
    3b. Python + Twisted:
    3425 Requests/sec => 102781 requests in 30s
    3c. Python + Flask:
    11751 Requests/sec => 16393 requests in 30s 
    Average Latency 55.54ms
    4. Node + Express: 
    8962 Requests/sec => 268866 requests in 30s
    Average Latency 7.14ms
```

Source: https://medium.com/@tschundeee/express-vs-flask-vs-go-acc0879c2122

---
