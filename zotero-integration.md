# {{title}} 
##### {{authors}} {{date | format ("YYYY")}}
[PDF]({{pdfZoteroLink}})

>[!ABSTRACT]- **Abstract**
>{{abstractNote}}

> [!info]- **Info**
> [Zotero-Link]({{zoteroSelectURI}})
> [URL]({{url}})
> [DOI]({{doi}})
> **Related** {% for relation in relations -%} {%- if relation.citekey -%} [[{{relation.citekey}}]], {% endif -%} {%- endfor%}
> **Bibliography** {{bibliography}}

> [!tip|key]- **Annotation Key**
> > [!quote|Bluehighlight] ℹ Background Information, Prerequisites
> 
> > [!quote|Redhighlight] ❓ Assumptions, Questions, Goals, Problems
>
> > [!quote|Purplehighlight] 📊 Main Findings, Results, Conclusions
>
> > [!quote|Greenhighlight] 🧪Experimental Details or Methods
>
> > [!quote|Yellowhighlight] ⭐ Interesting Point, Facts, Examples
>
> > [!quote|Orangehighlight] 🚨 Disagree with Author
>
> > [!quote|Greyhighlight] 📔 Vocabulary, Names, Dates, Definitions
>
> > [!quote|Magentahighlight] 📑 Interesting References

---
## General Notes
{% persist "notes" %}
#### Skim Summary

#### Take-Home Message

#### Interesting Figures

{% endpersist %}

---
## Reading Notes
{% persist "annotations" %}

{%-
    set zoteroColors = {
	    "#2ea8e5": "blue",
        "#ff6666": "red",
        "#a28ae5": "purple",
        "#5fb236": "green",
        "#ffd400": "yellow",
        "#f19837": "orange",
        "#aaaaaa": "grey",
        "#e56eee": "magenta"
    }
-%}
{%-
   set colorHeading = {
		"blue": "ℹ Background Information, Prerequisites",
		"red": "❓ Assumptions, Questions, Goals, Problems",
		"purple": "📊 Main Findings, Results, Conclusions",
		"green": "🧪Experimental Details or Methods",
		"yellow": "⭐ Interesting Point, Facts, Examples",
		"orange": "🚨 Disagree with Author",
		"grey": "📔 Vocabulary, Names, Dates, Definitions",
		"magenta": "📑 Interesting References"
   }
-%}

{%- set newAnnot = [] -%}
{%- set newAnnotations = [] -%}
{%- set annotations = annotations | filterby("date", "dateafter", lastImportDate) %}

{% if annotations.length > 0 %}
*Imported: {{importDate | format("YYYY-MM-DD HH:mm")}}*

{%- for annot in annotations -%}

    {%- if annot.color in zoteroColors -%}
        {%- set customColor = zoteroColors[annot.color] -%}
    {%- elif annot.colorCategory|lower in colorHeading -%}
    	{%- set customColor = annot.colorCategory|lower -%}
    {%- else -%}
	    {%- set customColor = "other" -%}
    {%- endif -%}

    {%- set newAnnotations = (newAnnotations.push({"annotation": annot, "customColor": customColor}), newAnnotations) -%}

{%- endfor -%}

{#- INSERT ANNOTATIONS -#}
{#- Loops through each of the available colors and only inserts matching annotations -#}
{#- This is a workaround for inserting categories in a predefined order (instead of using groupby & the order in which they appear in the PDF) -#}

{%- for color, heading in colorHeading -%}
{%- for entry in newAnnotations | filterby ("customColor", "startswith", color) -%}
{%- set annot = entry.annotation -%}

{%- if entry and loop.first %}

### {{colorHeading[color]}}
{%- endif %}

> [!quote|{%- if colorHeading[color] != "📊 Main Findings, Results, Conclusions" -%}{{annot.colorCategory}}{{annot.type}}{%- else -%}{{"Purple" + annot.type}}{%- endif -%}] {{annot.annotatedText|nl2br}}{{annot.ocrText}} [p. {{annot.pageLabel}}](zotero://open-pdf/library/items/{{annot.attachment.itemKey}}?page={{annot.pageLabel}}&annotation={{annot.id}})
> {% if annot.hashTags %}{{annot.hashTags}}{% endif -%}

{%- if annot.comment %}
> **{{annot.comment|nl2br}}**
{%- endif -%}

{%- endfor -%}
{%- endfor -%}
{% endif %}
{% endpersist %}



