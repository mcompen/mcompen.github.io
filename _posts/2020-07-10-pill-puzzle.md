---
layout: post
title: "Pill puzzle"
description: "The pill puzzle is about statistical questions in the scenario that one takes half a pill a day from a box containing whole pills. If the pill is whole, it is broken in half and one half is returned to the box while the other is consumed. If it is half, it is simply consumed."
comments: false
mathjax: true
keywords: "pill box puzzle half whole statistics probability"
---


This story is about a sad man. The man is ordered by his doctor to take one pill each day in order to stay happy. The doctor gives him a pill box with $N$ pills. The sad man decides in his sadness that he wants only to be slightly happy. Therefore, he takes only half the dosage each day.

On day 1 he randomly grabs a whole pill from the box, he breaks it in half and consumes one half while putting the other half back in the box. On day 2 he might either grab one of the $N-1$ whole pills with probability $P(X_2=W) = \frac{N-1}{N}$, or the 1 half pill with probability $P(X_2=H) = \frac{1}{N}$. If he gets the half pill, he simply consumes it. $2\cdot N$ days pass before the bottle is empty.

This story leads to a set of interesting statistical questions:
1. If this experiment is done by all sad men in the entire world, what is the average number of days that will pass before only halves remain inside the bottle?
2. The first day the pill will be whole. The last day the pill will be half. But how does the probability $P(X_k=H)$ behave between day $1$ and day $2\cdot N$?

Before we analyze this problem mathematically, it is too tempting to write some quick and dirty code and make some figures.

```julia
function experiment(w_arr::Array{Int, 2}, h_arr::Array{Int, 2}, day::Int, man::Int)
    prob_w = w_arr[day, man] / (h_arr[day, man] + w_arr[day, man])
    if rand() < prob_w   # Taken a whole: split it, and return one half
        w_arr[day + 1, man] = w_arr[day, man] - 1
        h_arr[day + 1, man] = h_arr[day, man] + 1
    else  # Taken a half: consume it
        w_arr[day + 1, man] = w_arr[day, man]
        h_arr[day + 1, man] = h_arr[day, man] - 1

    end
end
```


```julia
function main(N_pills::Int, N_men::Int)
    N_days = 2 * N_pills
    w_arr = zeros(Int, N_days, N_men)
    h_arr = similar(w_arr)

    w_arr[1, :] .= N_pills

    for i=1:N_men
        for j=1:N_days-1
            experiment(w_arr, h_arr, j, i)
        end
    end
    return w_arr, h_arr
end
```

We can track the development of the contents of the pill box with e.g. 100 pills and a million sad men by using

```julia
wholes, halves = main(100, Int(1e6));
```

Plotting these results gives the following interesting figures:

| ![svg](/assets/svg/100-pills-1000000-trials-halves.svg) |
|:--:|
| *The average (solid red) number of halves, standard deviation (ribbon), and 10 example trajectories.* |

| ![svg](/assets/svg/100-pills-1000000-trials-wholes.svg) |
|:--:|
| *The same figure for wholes* |
