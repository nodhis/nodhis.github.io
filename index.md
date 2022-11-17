---
layout: page
mathjax: true
---

[Euler's formula](https://en.wikipedia.org/wiki/Euler%27s_formula) relates the  complex exponential function to the trigonometric functions.

$$ e^{i\theta}=\cos(\theta)+i\sin(\theta) $$

The [Euler-Lagrange](https://en.wikipedia.org/wiki/Lagrangian_mechanics) differential equation is the fundamental equation of calculus of variations.

$$ \frac{\mathrm{d}}{\mathrm{d}t} \left ( \frac{\partial L}{\partial \dot{q}} \right ) = \frac{\partial L}{\partial q} $$

The [Schrödinger equation](https://en.wikipedia.org/wiki/Schr%C3%B6dinger_equation) describes how the quantum state of a quantum system changes with time.

$$ i\hbar\frac{\partial}{\partial t} \Psi(\mathbf{r},t) = \left [ \frac{-\hbar^2}{2\mu}\nabla^2 + V(\mathbf{r},t)\right ] \Psi(\mathbf{r},t) $$

{% highlight python %}

			if i.isupper(): 
				diff = ord(i) - ord('A')
				new_diff = (diff + key_pos) % 26
				new_pos = new_diff + ord('A')
				print(chr(new_pos), end="")
			 
			elif i.islower():
				diff = ord(i) - ord('a')
				new_diff = (diff + key_pos) % 26
				new_pos = new_diff + ord('a')
				print(chr(new_pos), end="")	
		
			elif i.isdigit():
				diff = ord(i) - ord('0')
				new_diff = (diff + key_pos) % 10
				new_pos = new_diff + ord('0')
				print(chr(new_pos), end="")
			else:

{% endhighlight %}
