---
layout: post
title: "Web: Webiste-Tools"
subtitle: ""
date: 2017-09-17
author: Sol
category: Web
tags: tools
finished: false
mathjax: true
---

### Idees a implementer
* [plot.ly](https://plot.ly/)


## Mathjax
Le site utilise [MathJax](https://github.com/mathjax/MathJax) pour génénrer le rendu des expressions mathématiques.

* Expression within `$...$` or `\(...\)` will be rendered inline.
* Expression within `$$...$$` or `\[...\]` or will be rendered in block.

In line: $f(x) = sin(x) + b^2$
Bloc: $$ \frac{2}{3}+4x $$


## Mermaid
Le site utilise [mermaid](https://mermaidjs.github.io) pour générer des diagrames (Flow, Sequence, Gantt, UML).

### Installation
1. Download js code of mermaid: [mermaid](https://github.com/knsv/mermaid). ATM, the file is hosted [here](https://unpkg.com/mermaid@7.1.0/dist/) but it might change after update. We want the file named `mermaid.min.js`.
2. Put this file into a folder named `js` in the root of the website.
3. Open `/_includes/head.html` and add this line: 
```html
<script src="/js/mermaid.min.js"></script>
```
4. Now we can make nice graphs like this one:
<div class="mermaid">
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
</div>
```html
<div class="mermaid">
graph TD;
    A-->B;
    A-->C;
    B-->D;
    C-->D;
</div>
```

We just need to put the graph code inside `<div class="mermaid">...</div>` tags.

* [mermaid quick start](https://github.com/knsv/mermaid)
* [mermaid doc](https://mermaidjs.github.io)


## Code chunk

```python {cmd=true matplotlib=true}
import matplotlib.pyplot as plt
plt.plot([1,2,3, 4])
plt.show() # show figure
```



