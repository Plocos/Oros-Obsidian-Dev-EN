---
Query: ""
---
# Categories Index

Search: `INPUT[text(placeholder(Category name)):Query]`

```dataviewjs
var query = dv.current().file.frontmatter.Query

var results = dv.pages('"300 - Resources/310 - Categories" and -"300 - Resources/310 - Categories/+ Categories Index"').file.where(r => {
	if(query != null){
		if(!r.name.toLowerCase().contains(query.toLowerCase())){
			return false;
		}
	}
	return true;
}).sort(k => k.inlinks.length, 'desc')


dv.list(results.text)
dv.paragraph(results.text)

dv.header(4, "Results")
if(results.length === 0) {
	dv.el("p", "No results to show")
} else {
	dv.table(["Categories", "Associated notes"], results.map(r => [r.link, "Associated notes: " + r.inlinks.length]));
}

```
