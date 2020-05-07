# lobby_edge2vec
Edge embedding to measure distance between two edges 

## Notions
Cash flows are represented in a directed graph `who gives how much to whom`. Then the recipient node also pays his income to another node. Then I'd like to figure out how much those two edges are related - in other word, `X% of this money money flow originated from this one`. 

## Resources
Learning Edge Representations via Low-Rank Asymmetric
Projections https://arxiv.org/pdf/1705.05615.pdf

## Known facts
- In general, randomwalk methods outperform “eigen” methods on producing vector representations that preserve the graph structure.
