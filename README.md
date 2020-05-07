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

1. Get `_client_uuid`
- assume we are interested in the company named "Ameriquest Mortgage"

```sql
select *
from lobbying_clients__infogroup_companies.clients__infogroup_id___rough ciir 
where ciir.client_full_name_used ilike '%ameriquest%'
>>> "_client_uuid"	client_full_name_used	infogroup_id
>>> 8f0f26aa-6410-5f0e-9d4b-2229d5324ac8	AMERIQUEST MORTGAGE COMPANY	424328982
```

2. Client pays `money` to `lobbyists` or `registrants` or Self-cients pays(expenses) for its own lobbying purpose
2-1. Client pays `money` to `lobbyists`
```sql
select c."client_full_name", regist."registrant_full_name", r.amount, i.issue_code, ic.issue_code_description 
from consolidated_layer_reports.reports r 
	inner join consolidated_layer_reports.clients as c using ("_client_uuid" )
	inner join consolidated_layer_reports.registrants as regist using ("_registrant_uuid")
	inner join consolidated_layer_reports.issues as i using ("_report_uuid")
	inner join consolidated_layer_reports.issues_code__reference as ic using ("issue_code")
where c.client_full_name ILIKE '%Ameriquest%'
order by amount desc
>>>client_full_name	registrant_full_name	amount	issue_code	issue_code_description
>>>AMERIQUEST MORTGAGE CO	OGILVY GOVERNMENT RELATIONS	$240,000.00	BAN	Banking
```

