---
layout: post
title: Swinging Clojure
---
Having played with threading, I thought I’d have a go with Java integration. Swing was the obvious target, and a simple quote fetcher was the result.

For simplicity, I stuck with URLConnection, but there are a number of Clojure libraries to solve the HTTP problem. All of them are somewhat nicer to deal with.

I also completely failed to get apply working with addItem on the ComboBoxes – I’m assuming PEBKAC at present, given my limited knowledge of the integration.

Still, the Swing integration was surprisingly easy to do – I had envisioned nightmares here, given the variation in style, but it proved not half as horrid as expected.
```
(import
  '(java.net URL URLConnection)
  '(java.io BufferedReader InputStreamReader)
  '(javax.swing JFrame JLabel JTextField JButton JComboBox BorderFactory)
  '(java.awt.event ActionListener)
  '(java.awt GridBagLayout GridBagConstraints Insets)
  )

(def currencies ["EUR" "GBP" "JPY" "USD"])

(defn build-url [from-code to-code]
  (str "http://quote.yahoo.com/d/quotes.csv?s="
    from-code to-code "=X&f=sl1d1t1c1ohgv&e=.csv")
  )

(defn read-file [input-stream]
  (loop [line (.readLine input-stream)
         content []]
    (if (= line nil)
      content
      (recur (.readLine input-stream) (concat content [line]))
      )
    )
  )

(defn get-quote [quote-string]
  (nth (.split quote-string ",") 1))

(defn read-url [url]
  (let [connection (.openConnection (URL. url))]
    (read-file (BufferedReader. (InputStreamReader.
      (.getInputStream connection)))))
  )

(defn set-currencies [combo-box]
  (loop [current (first currencies)
         remainder (rest currencies)]
    (if (not (= nil current))
      (do
        (.addItem combo-box (str current))
        (recur (first remainder) (rest remainder))
        )
      )
    )
  )

(defn quote-fetcher []
  (let [frame (JFrame. "Quote Fetcher")
        button (JButton. "Get Quote")
        from-label (JLabel. "From:")
        from-field (JComboBox.)
        to-label (JLabel. "To:")
        to-field (JComboBox.)
        value-label (JLabel. "Result:")
        value-field (JTextField.)]

    (.addActionListener button
      (proxy [ActionListener] []
        (actionPerformed [e]
          (let [from-code (.getSelectedItem from-field)
                to-code (.getSelectedItem to-field)]
            (.setText value-field
              (get-quote (first (read-url
                (build-url from-code to-code)))))
            )
          )
        )
      )

    (.setEditable value-field false)

    (set-currencies from-field)
    (set-currencies to-field)

    (.setBorder (.getContentPane frame)
      (BorderFactory/createEmptyBorder 10 10 10 10))

    (doto frame
      (.setLayout (GridBagLayout.))
      (.add from-label (GridBagConstraints. 0 0 1 1 0.0 0.0
        (GridBagConstraints/EAST) (GridBagConstraints/NONE) (Insets. 4 4 4 4) 0 0))
      (.add from-field (GridBagConstraints. 1 0 1 1 1.0 0.0
        (GridBagConstraints/EAST) (GridBagConstraints/HORIZONTAL) (Insets. 4 4 4 4) 0 0))
      (.add to-label (GridBagConstraints. 0 1 1 1 0.0 0.0
        (GridBagConstraints/EAST) (GridBagConstraints/NONE) (Insets. 4 4 4 4) 0 0))
      (.add to-field (GridBagConstraints. 1 1 1 1 1.0 0.0
        (GridBagConstraints/EAST) (GridBagConstraints/HORIZONTAL) (Insets. 4 4 4 4) 0 0))
      (.add button (GridBagConstraints. 0 2 2 1 0.0 0.0
        (GridBagConstraints/EAST) (GridBagConstraints/NONE) (Insets. 4 4 4 4) 0 0))
      (.add value-label (GridBagConstraints. 0 3 1 1 0.0 0.0
        (GridBagConstraints/EAST) (GridBagConstraints/NONE) (Insets. 4 4 4 4) 0 0))
      (.add value-field (GridBagConstraints. 1 3 1 1 1.0 1.0
        (GridBagConstraints/EAST) (GridBagConstraints/HORIZONTAL) (Insets. 4 4 4 4) 0 0))
      (.setSize 300 176)
      (.setVisible true)
      )
    )
  )

(quote-fetcher)
```