+++
title = "Доверительный интервал для экспонециального распределения"
date = 2021-04-27
description = "Границы доверительных интервалов для интенсивности отказов."
slug = "confidence-interval-for-exp-distribution"
aliases = ["/2021/04/27/confidence-interval-for-exp-distribution.html"]

[extra]
math = true
+++

$\lambda$ - интенсивность отказов

$1-\alpha$ - вероятность попадания в доверительный интервал

### Цензурирование выборки на основе количества событий(отказов)

Границы двустороннего доверительного интервала:

$$ Pr \left( \frac{\chi^2(\frac{\alpha}{2},2n)}{2T} 
\leq \lambda \leq
\frac{\chi^2(1-\frac{\alpha}{2},2n))}{2T}\right)
=1-\alpha $$

Верхняя граница одностороннего доверительного интервала:

$$ Pr \left( 0
\leq \lambda \leq
\frac{\chi^2(1-\alpha,2n)}{2T}\right)
=1-\alpha $$

### Цензурирование выборки на основе времени наблюдения
Границы двустороннего доверительного интервала:

$$ Pr \left( \frac{\chi^2(\frac{\alpha}{2},2(n+1)}{2T} 
\leq \lambda \leq
\frac{\chi^2(1-\frac{\alpha}{2},2(n+1))}{2T}\right)
=1-\alpha $$

Верхняя граница одностороннего доверительного интервала:

$$ Pr \left( 0
\leq \lambda \leq
\frac{\chi^2(1-\alpha,2(n+1))}{2T}\right)
=1-\alpha $$
