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
- **Assume we are interested in the company named `Ameriquest Mortgage`**

```sql
select *
from lobbying_clients__infogroup_companies.clients__infogroup_id___rough ciir 
where ciir.client_full_name_used ilike '%ameriquest%'
>>> "_client_uuid"	client_full_name_used	infogroup_id
>>> 8f0f26aa-6410-5f0e-9d4b-2229d5324ac8	AMERIQUEST MORTGAGE COMPANY	424328982
```

2. Client pays `money` to `lobbyists` or `registrants`



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

2-1. Or, sometimes self pays(expenses) to oneself for its own lobbying purpose
```sql
-- with the same query above,
>>>client_full_name	registrant_full_name	amount	issue_code	issue_code_description
>>>AMERIQUEST MORTGAGE CO	AMERIQUEST MORTGAGE CO	$20,000.00	BAN	Banking
```
<p align="center"> <img src="Screen%20Shot%202020-05-08%20at%202.37.44%20AM.png"/> </p>

2-2. Thus, more generally, under the context of lobbying, entities are giving money from one to another, and also, gives money to itself. 

<p align="center"> <img src="Screen%20Shot%202020-05-08%20at%203.03.13%20AM.png"/> </p>

Remember, `Money` means `On Behalf Of`.

3. `PAC` and its `Legislative Activity`

Now we want to know which `PAC` server for which purpose. For example, we want to know what `Halvorson for Congress` does in the congress and why/how do they get those money from `Safari Club Int'l` via `Ogilvy` upon their legislative action. To do that, we need to find who's the recipient of the 

```sql
select distinct cc.sub_id, c.committee_name, c.committee_id, c.report_year, cc.report_year, cc.candidate_id, cd.candidate_name, cc.tran_amount
from relational_layer_campaign.committee___clean c
	inner join relational_layer_campaign.committee_to_candidate_contribution___clean cc on cc.committee_id = c.committee_id and cc.report_year = c.report_year
	inner join relational_layer_campaign.candidate cd on cd.candidate_id= c.candidate_id and cd.report_year = c.report_year 
where c.committee_name ilike '%halvorson for congress%'
>>> sub_id	committee_name	committee_id	report_year	report_year	candidate_id	candidate_name	tran_amount
>>> 4050220131188538966	HALVORSON FOR CONGRESS	C00539932	2014	2014	H8IL11113	HALVORSON, DEBORAH L	$1,000.00
4050220131188538969	HALVORSON FOR CONGRESS	C00539932	2014	2014	H8AZ01104	HALVORSON, DEBORAH L	$1,000.00
4050720091114699887	HALVORSON FOR CONGRESS	C00440016	2008	2008	H8LA04241	HALVORSON, DEBORAH 'DEBBIE'	$2,000.00
4072920111141824811	HALVORSON FOR CONGRESS	C00440016	2012	2012	H6NC08111	HALVORSON, DEBORAH	$2,500.00
```

or, more simply just using ld203

```sql
select cd.organization_name, cb.contributor_name, cb.payee_name, cb.amount, cb.recipient_name 
from relational___campaign.contributiondisclosure cd
	inner join relational___campaign.contributions cb using ("ld203_uuid")
where cd.organization_name ilike '%Ogilvy%' and cb.contributor_name ilike '%Safari%' and cb.payee_name ilike '%halvorson%'
order by amount desc
>> organization_name	contributor_name	payee_name	amount	recipient_name
>> Ogilvy Government Relations	Safari Club International PAC	Halvorson For Congress	$5,000.00	Deborah L. Halvorson
```
