Abstract - Need to add a bit of emphasis on the diff from SMT solvers

Introduction - Make sure we reference all the egg stuff including but not limited to:
egg, egglog, eggsmall, chandras inference, Theory exploretaion a la carte, Tensor rewrites, herbie, Synthesizing structured CAD models with equality saturation and inverse transformations,Vectorization for digital signal processors via equality saturation, Guided equality saturation. - V (sort of)
To see which of these could benefit from colors we should probably discuss further with chatGPT. - X (Probably not needed with better exploratory explanation)

It would be pretty straight forward to claim many are actually optimizaers, and give an example where SMT solvers are not relelvant and won't help to backtrack.
Example to counter SMT style reasoning:
We are in the middle of optimization phase. 
We are seeking equivalent programs that are more optimal considering a metric M (lower is better). 
In the presence of conditional expression if b P_1 else P_2, we could find that $b \implies M(P'_1) < M(P_1)$.
During a backtracking style of reasoning, we would need to replace (or save) $P'_1$ before backtracking.
Now doing the same we would get $P'_2$.
But, this type of reasoning would lose potential non-local optimizations.
For example, consider a situation that $\exists P_3. b \implies P_1 = P_3 \land \lnot b \implies P_2 = P_3$ leading to $b$ being unused.
Potentially this global optimization is more efficient, but it will be undetectable in a local (backtracking) type of reasoning.
This means optimizations are also exploratory - we don't know what would be a result ahead of time.

How do we use this example without actually doing experiments on optimization?

A better definition of exploratory reasoning tasks:
The goal is unkown, but rather we look for the best we can find.
Will involve (I want this to be mandatory, but am having a hard time to explain why) some metric to choose the best solution.

Proof search is not an exploratory task by itself, as we do not care which proof was found.
But, focusing on sub-goals is because it is not known ahead of time which will be the one to help. 
The goal would be to advance the proof, and we will rely on some metric to choose. 
Backtracking will "lose" the previous proof context?

Lemma inference is an exploratory task, as we don't know the goal ahead of time, and although a metric is not strictly necessary, it is essential for an efficient seach.
What would prevent us from "keeping" the resulting sub-goals in a list and work from it?
We will need to continue the work after proving a sub-goal here

Program optimization is an exploratory task as we don't know which programs are equivalent, and we care about choosing well.
Gave an exmaple on why "throwing" contexts is bad, and holding all equivalent programs is exponential?

Key observation could be improved (textually)

We extend the e-graph..... (What we did section) HAS to contain a reference to the optimization work

Fix any reference to the algorithms now in the appendix
Add a bit of introductory text to the algorithms in the appendix


-----  Old  ------

Things for experiments:
1. Fix bug with black classes merge - V
2. Profile - V
3. FMCAD experiments with deep splits
4. FMCAD experiments with less memory - V

Things to write:
* Move to oopsla document class - V
1. Add mappings used to system description
    a. Colors
        I. Equality Classes
        II. Union find
        III. Black "colored" class
        IV. Color predecessors
        V. Color children
    b. Colored equivalences
    c. Colored Parents
    d. Colored Memo
    e. Base colors
    
2. Add a formal algorithm for search, union, and rebuild
    a. Union needs to update sets (equality classes). Current implementation goes down hierarchy, but that is not necessary
    b. Search uses multipattern + Jump (maybe do additional Or and Not)
    c. Rebuild rebuilds hashcons each time, and has additional tracking on what to delete.
3. Add description on hierarchy
   a. Remove current excuses
   b. Add description in memory optimizations
   c. Add descripyion on rebuild
   d. Add description on e-matching
   e. Add description and appoligize for union
4. Add a bit in overview on the adaptation to DAG

Action items:
    * In introduction add in the initial clone examples more emphasis on hierarchy
    * 


The datastructure is actually the extension to the E-Graph:
Additional mappings used in the system
    a. Colors - mapping from color id to:
        I. Equality Classes (EClass-Id to Set<EClass-Id>) - All the eclasses that are part of the equality class in this color
        II. Union find - To represent the equality relation
        III. Black "colored" class (EClass-Id to EClass-Id) - For each class in the equality relation of this color there is at most one E-Class containing colored E-Nodes. This mapping is to find it easily.
        IV. Color predecessors (Array of Color-Id) - All the colors that are higher then the current one in the hierarchy. Used to filter E-Matching results, and find parents in rebuild.
        V. Color children (Array of Color-Id)- All the colors that directily inherit from this color
    b. Colored equivalences (Id to Color-Id) - For each E-Class id in the e-graph holds which colors have equivalences to it. This is more memory efficient then holding all the equivalences, and they can be found under each color.
    c. Colored Memo (Color-Id to (Enode to EClass-Id))  - Same as the E-Graph hash-cons (or memo) but for each color holding only the colored Enodes.
    d. Base colors - Colors which directly inherit their equality relation from the original E-Graph
    e. For EClass -  added Colored Parents (Color-Id to Array of E-node and parent Eclass Id)- Same as parents in a normal E-Graph, but to track the colored E-Nodes.