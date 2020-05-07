# lobby_edge2vec
Edge embedding to measure distance between two edges 

## Notions
Cash flows are represented in a directed graph `who gives how much to whom`. Then the recipient node also pays his income to another node. Then I'd like to figure out how much those two edges are related - in other word, `X% of this money money flow originated from this one`. 

## Resources
Learning Edge Representations via Low-Rank Asymmetric
Projections https://arxiv.org/pdf/1705.05615.pdf

## Known facts
- In general, randomwalk methods outperform “eigen” methods on producing vector representations that preserve the graph structure.

## List of information about who gives money to whom

Corporation pays to `Registrants` or its `in-house` lobbying team.
For example, if the company is `Ameriquest` 

```sql
select *
from lobbying_clients__infogroup_companies.clients__infogroup_id___rough ciir 
where ciir.client_full_name_used ilike '%ameriquest%'
>>> "_client_uuid"	client_full_name_used	infogroup_id
>>> 8f0f26aa-6410-5f0e-9d4b-2229d5324ac8	AMERIQUEST MORTGAGE COMPANY	424328982
```
