# NLP Exam Answers: Lexical Ambiguity, WSD and Anaphora Resolution

## Q.1: \"The bank will not open until the river recedes.\" (CO3, 1+1+1)

### a) Type of lexical ambiguity

This is **homonymy**, a form of **sense ambiguity**. The word \"bank\"
has unrelated meanings: a financial institution and the land alongside a
river. The senses share spelling and pronunciation but are
etymologically unrelated. Both senses are plausible for \"will not
open\" (a financial bank opening for business, or a flooded riverbank
area opening to access), so the sentence is genuinely ambiguous without
context.

### b) How WordNet or BabelNet helps

-   WordNet lists the synsets of \"bank\" (e.g., *bank#1: financial
    institution*, *bank#2: sloping land beside water*), each with a
    gloss and example sentences.

-   A **Lesk-style overlap** between the context words (\"river\",
    \"recedes\", \"open\") and each gloss favors the sense *sloping land
    beside a body of water*, since \"river\" appears in that gloss.

-   Relations such as hypernyms and meronyms (river and bank are both
    linked to bodies of water) give semantic-relatedness evidence.

-   BabelNet adds multilingual and encyclopedic knowledge (Wikipedia,
    etc.), which helps with rarer senses and cross-lingual
    disambiguation.

### c) Why automatic systems might still fail

-   The sentence is genuinely ambiguous: a financial bank might also be
    \"closed until the river recedes\" because of flooding, so \"river\"
    does not rule out the financial sense.

-   Gloss overlap is sparse, because short glosses share few words with
    the context.

-   Sense inventories are fine-grained and may miss new or
    domain-specific senses.

-   Pragmatic and world knowledge (floods close buildings) is needed,
    which dictionary-based methods lack.

## Q.2: Naïve Bayes for Word Sense Disambiguation (CO4, 1.5+1+1.5)

### a) Applying Naïve Bayes to WSD

WSD is treated as a classification problem: choose the sense ŝ of a
target word w given context features f₁ ... fₙ.

**ŝ = argmax over senses s of P(s) · ∏ P(fⱼ \| s)**

-   **Features:** surrounding words (bag-of-words in a window),
    collocations (e.g., the word at position ±1), POS tags.

-   **Training:** from a sense-annotated corpus (e.g., SemCor), estimate
    P(s) as the relative frequency of each sense, and P(fⱼ \| s) by
    counting co-occurrence of feature and sense, with smoothing (e.g.,
    Laplace).

-   **Prediction:** compute the score for each sense and pick the
    highest.

-   The \"naïve\" assumption is that the features are conditionally
    independent given the sense.

### b) Limitations compared to Lesk\'s approach

-   **Needs labeled data:** Naïve Bayes requires a large sense-annotated
    corpus, which is expensive to create. Lesk uses only a dictionary or
    WordNet glosses, so it is knowledge-based and needs no training
    data.

-   **Data sparsity:** rare senses and unseen words get poor estimates;
    Lesk can still handle any word that has a gloss.

-   **Independence assumption:** context words are correlated, which
    Naïve Bayes ignores.

-   **Domain dependence:** a model trained on one domain transfers
    poorly, whereas Lesk is domain-independent.

-   **One model per word:** typically a separate classifier is needed
    for each target word.

### c) Improvement using Yarowsky\'s semi-supervised algorithm

Use **bootstrapping** to reduce the labeling need:

1.  Start with a small set of **seed labeled examples** (or seed
    collocations, e.g., \"bank + deposit\" → financial).

2.  Train a classifier (decision list or Naïve Bayes) on the seeds.

3.  Apply it to the **unlabeled corpus** and keep only high-confidence
    predictions.

4.  Add those to the training set and retrain; repeat until convergence.

Yarowsky\'s method relies on two heuristics: **one sense per
collocation** and **one sense per discourse**, which propagate labels
reliably. This gives accuracy close to supervised methods with far less
annotation.

## Q.3: \"John met Alex after he finished the project.\" (CO5, 1+1+1)

### a) Anaphora and why resolution is difficult

-   **Anaphor:** the pronoun **\"he\"**. Its **antecedent** is either
    **John** or **Alex**.

-   **Why it is difficult:**

    -   Both candidates match in gender, number and person, so agreement
        constraints cannot decide.

    -   Both are plausible subjects of \"finished the project\", so
        resolution needs world or discourse knowledge.

    -   Syntax alone is compatible with either reading; the sentence is
        truly ambiguous out of context.

    -   A preference for the subject (John) is only a heuristic and can
        be wrong.

### b) How Hobbs\' algorithm resolves \"he\"

Hobbs\' algorithm is a **syntactic, tree-walking method**:

1.  Start at the NP node dominating the pronoun \"he\" in the parse
    tree.

2.  Go up to the first S or NP node (call it X) and traverse the tree
    **left-to-right, breadth-first**, below X to the left of the path,
    proposing any NP that matches in gender and number as the antecedent
    (excluding NPs that violate binding constraints).

3.  If none is found, move up to the next S node and repeat the search;
    if still none, look at **previous sentences**, traversing their
    trees left-to-right, breadth-first.

In this sentence, the traversal reaches the main-clause subject NP
**\"John\"** first, since subjects are higher and further left in the
tree than the object \"Alex\". Hobbs therefore proposes **John** as the
antecedent. This reflects a syntactic subject preference, but it cannot
confirm that the choice is actually correct.

### c) A scenario where syntactic constraints alone fail

Consider: *\"The city council refused the demonstrators a permit because
they feared violence.\"* versus *\"... because they advocated
violence.\"* (a Winograd-style example).

-   The syntax is **identical** in both, but \"they\" refers to the
    **council** in the first and the **demonstrators** in the second.

-   Resolving it requires **world knowledge and commonsense reasoning**
    (councils fear violence; demonstrators may advocate it), which
    syntactic constraints and Hobbs\' algorithm do not capture.

-   Real-world impact: in coreference for **chatbots, legal or medical
    document analysis, and machine translation**, a wrong antecedent can
    change the meaning, so semantic, pragmatic and discourse-coherence
    information is also needed.
