---
layout: post
title: 4. Configurando los atributos del índice
author: Rosario Santa Marina, Maira Diaz
usernames: [rosariosm maira1001001]
tags: [elasticsearch, mapping]
---


MAPPING + ANALYSER + SEARCH


## Mapping
Mapping es el proceso de definir como serán almacenados e indezados los campos del documento.

Si volvemos a los emplos del módulo 3, se configuraron por defecto los campos del índice `article_index`. Para visualizar la configuración, escriba en consola la siguiente consulta:

{% highlight bash %}
$ curl -XGET 'http://localhost:9200/article_index/_mapping?pretty'
{% endhighlight %}

Se visualizarán los siguientes datos:

{% highlight json %}
{
  "article_index" : {
    "mappings" : {
      "fantasy" : {
        "properties" : {
          "body" : {
            "type" : "string"
          },
          "created_at" : {
            "type" : "date",
            "format" : "dateOptionalTime"
          },
          "is_visible" : {
            "type" : "boolean"
          },
          "lead" : {
            "type" : "string"
          },
          "slug" : {
            "type" : "string"
          },
          "title" : {
            "type" : "string"
          }
        }
      }
    }
  }
}
{% endhighlight %}
 
Es posible modificar e incluso ampliar la configuración de los campos para poder realizar las búsquedas con mayor precisión.

------Buscar bien en que influye configurar bien el mapping!!!!!!!!!!!!!!!!!!!!!!!!1

Cada campo tiene las siguientes características


-----------------------------------------------------------------


Si deseamos buscar entre el 1° de Enero de 2014 y la fecha actual, realizamos la siguiente consulta:


{% highlight bash %}
curl -XGET 'localhost:9200/article_index/politics/_search?pretty' -d '
{
  "query" : {
    "range" : {
      "created_at" : {
        "gt" : "2014-01-01T00:00:00",
        "lt" : "now"
      }
    }
  }
}'

{% endhighlight %}

https://www.elastic.co/guide/en/elasticsearch/reference/current/query-filter-context.html


CLASIFICACIÓN:
------------------------------------------------------

2 tipos de clasificaciones: 

1. respecto de la sintaxis de la query

1.A. leaf clauses : match, term, range
1.B.  compund clases: 1.A, 1.B

2. respecto de la respuesta que nos da la query

2.A. QUERY CONTEXT: _score
2.B. QUERY FILTER: yes/no



QUERY CONTEXT: _score
--------------------------------------------------------
how well does this document match this query clases?

search(query) : query parameter




QUERY FILTER: yes/no
------------------------------------------------------
does this document match this query clases?

bool query:             filter    |
                        must      | parameter
                        must not  |
                        should    |

constant_score query:   filter    | parameter



ejemplos:

{% highlight bash %}
{
    "bool": {
        "must": { "match":   { "email": "business opportunity" }},
        "should": [
            { "match":       { "starred": true }},
            { "bool": {
                "must":      { "match": { "folder": "inbox" }},
                "must_not":  { "match": { "spam": true }}
            }}
        ],
        "minimum_should_match": 1
    }
}
{% endhighlight %}
