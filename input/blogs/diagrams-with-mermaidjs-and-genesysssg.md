title: Diagrams with mermaidjs and GenesysSSG
created: Sunday, July 2, 2023
tags:
  - genesysSSG
  - mermaidjs
short: Diagrams with mermaidjs and GenesysSSG
---
# Diagrams with mermaidjs and GenesysSSG

Having diagrams on your site is always nice.

One of my favorite tools to create diagrams is [mermaidjs](https://mermaid.js.org/)

I wonted to investigate how easy would it be to integrate mermaid js with (genesysSSG)[https://dejandjenic.github.io/projects/genesysssg]

Lets check some examples of mermaidjs from this [link](https://mermaid.js.org/syntax/examples.html)

Create new blog post page with genesysSSG by running

```
genesysSSG -b "Test mermaidjs"
```


Copy and paste a few examples from mermaidjs


<pre>
```mermaid
 pie title What Voldemort doesn't have?
         "FRIENDS" : 2
         "FAMILY" : 3
         "NOSE" : 45
```
</pre>



<pre>
```mermaid
sequenceDiagram
    Alice ->> Bob: Hello Bob, how are you?
    Bob-->>John: How about you John?
    Bob--x Alice: I am good thanks!
    Bob-x John: I am good thanks!
    Note right of John: Bob thinks a long<br/>long time, so long<br/>that the text does<br/>not fit on a row.

    Bob-->Alice: Checking with John...
    Alice->John: Yes... John, how are you?
```
</pre>


<pre>
```mermaid
graph LR
    A[Square Rect] -- Link text --> B((Circle))
    A --> C(Round Rect)
    B --> D{Rhombus}
    C --> D
```
</pre>



<pre>

```mermaid
graph TB
    sq[Square shape] --> ci((Circle shape))

    subgraph A
        od>Odd shape]-- Two line<br/>edge comment --> ro
        di{Diamond with <br/> line break} -.-> ro(Rounded<br>square<br>shape)
        di==>ro2(Rounded square shape)
    end

    %% Notice that no text in shape are added here instead that is appended further down
    e --> od3>Really long text with linebreak<br>in an Odd shape]

    %% Comments after double percent signs
    e((Inner / circle<br>and some odd <br>special characters)) --> f(,.?!+-*ز)

    cyr[Cyrillic]-->cyr2((Circle shape Начало));

     classDef green fill:#9f6,stroke:#333,stroke-width:2px;
     classDef orange fill:#f96,stroke:#333,stroke-width:4px;
     class sq,e green
     class di orange
```
</pre>



<pre>
```mermaid
sequenceDiagram
    loop Daily query
        Alice->>Bob: Hello Bob, how are you?
        alt is sick
            Bob->>Alice: Not so good :(
        else is well
            Bob->>Alice: Feeling fresh like a daisy
        end

        opt Extra response
            Bob->>Alice: Thanks for asking
        end
    end
```
</pre>

add mermaidjs initialization 

<pre>
&lt;script type=&quot;module&quot;&gt;
  import mermaid from &#39;https://cdn.jsdelivr.net/npm/mermaid@10/dist/mermaid.esm.min.mjs&#39;;

  mermaid.initialize({
  securityLevel: &#39;loose&#39;,
});

&lt;/script&gt;
</pre>

run genesysSSG locally 

```
genesysSSG -s -w
```

and check the results.

wanna see this page on live demo? check this [link](mermaid-demo.html)

how easy and how awesome that was!