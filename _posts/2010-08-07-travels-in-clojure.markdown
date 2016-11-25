---
layout: post
title: Travels in Clojure
---
I’m currently reading through Pragmatic’s [Seven Languages in Seven Weeks](http://pragprog.com/titles/btlang/seven-languages-in-seven-weeks). After every chapter it has a series of small ‘self-study’ tasks. For Clojure, the final of these is the [Sleeping Barber problem](http://en.wikipedia.org/wiki/Sleeping_barber_problem).

So, mostly for my own reference, I present my solution. If anyone who actually knows Clojure ever reads this, I’d welcome any suggestions.

As for Clojure, I’m still endlessly conflicted on whether Lisp is the most elegant language of them all, or if the endless mass of paranthesis is just syntactic pain. What I am sure of is that I wouldn’t trust many of the programmers I’ve run into with a language of its power. Mind, I don’t trust many of those same with the relatively limited power of Java.
```
(ns barber)

(def seats (ref (clojure.lang.PersistentQueue/EMPTY)))
(def the-barber (agent 0))

(defn barber [b]
    (dosync
        (if (peek @seats)
            (do
                (alter seats pop)
                (Thread/sleep 20)
                (inc b)
            )
            b
        )
    )
)

(defn enter-shop [customer]
    (dosync 
        (if (< (count @seats) 3)
            (alter seats conj customer)
        )
    )
)

(defn run-barber [milliseconds]
    (do 
        (add-watch seats :send-to-barber 
            (fn [_ _ _ _] (send-off the-barber barber))
        )
        
        (def end-time (+ milliseconds (System/currentTimeMillis)))
        
        (while (< (System/currentTimeMillis) end-time)
            (Thread/sleep (+ 10 (rand-int 20)))
            (send (agent :customer) enter-shop)
        )
        (await the-barber)
        @the-barber
    )
)

(run-barber 10000)
```