# ElasticQuery v2.3 [![PyPI version](https://badge.fury.io/py/ElasticQuery.svg)](https://pypi.python.org/pypi/ElasticQuery)

A simple query builder for Elasticsearch. Install with `pip install elasticquery`. Uses metod calls and their args/kwargs to generate query/filter/aggregate objects. Outputs dict/json represntation to be passed directly to ES.

### API

+ [ElasticQuery](./docs/client.rst)
+ [Filters](./docs/filters.rst)
+ [Queries](./docs/queries.rst)
+ [Aggregates](./docs/aggregates.rst)
+ [Suggesters](./docs/suggesters.rst)


## Synopsis

```py
from elasticsearch import Elasticsearch
from elasticquery import ElasticQuery, Filter, Query


q = ElasticQuery(
    es=Elasticsearch(),
    index='mapping_test',
    doc_type='doc_mapping'
)

# -> query.filtered.filter
q.filter(
    # -> query.filtered.filter.bool
    Filter.bool(must=[
        # -> query.filtered.filter.bool.must.terms
        Filter.terms('field', ['this', 'that'])
    ], must_not=[
        # -> query.filtered.filter.bool.must_not.or
        Filter.or_(
            # -> query.filtered.filter.bool.must_not.or.term
            Filter.term('another_field', 'matching-term'),
            # -> query.filtered.filter.bool.must_not.or.query.query_string
            Filter.query(
                Query.query_string('A QUERY STRING', default_operator='OR')
            )
        )
    ])
)

# -> query.filtered.query
q.query(
    # -> query.filtered.query.prefix
    Query.prefix('field', 'prefixed-')
)

# -> aggregates
q.aggregate(
    # -> aggregates.agg_name
    Aggregate.date_histogram('agg_name', 'date_field', '1d').aggregate(
        # aggregates.agg_name.aggregates.sub_agg_name
        Aggregate.terms('sub_agg_name', 'terms_field', size=50)
    )
)

# Print the query, then run on ES and print it's output
print q.json(indent=4)
print q.get()
```


## Naming Differences

ElasticQuery attempts to keep all names and arguments in-line with their ES coutnerparts. Unfortunately a number of key terms are reserved by Python, so ElasticQuery implements the following alternatives:

+ `from` -> `from_` (eg setting query['from'])
+ `or` -> `or_` (eg or_filters)
+ `and` -> `and_` (eg and_filters)


## Testing

+ Create virtualenv
+ `pip install requirements.pip`
+ Run `nosetests`
