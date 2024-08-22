---
Query: ""
---
# Notes Index

Search: `INPUT[text(placeholder(Note name)):Query]`

```dataviewjs
var query = dv.current().file.frontmatter.Query

var results = dv.pages('"300 - Resources/320 - Notes" and -"300 - Resources/320 - Notes/+ Notes Index"').file.where(r => {
	if(query != null){
		if(!r.name.toLowerCase().contains(query.toLowerCase())){
			return false;
		}
	}
	if(r.frontmatter.Source == null){
		r.frontmatter.Source = ""
	}else{
		r.frontmatter.Source = "Source: " + r.frontmatter.Source
	}
	return true;
})

dv.header(4, "Results")
if(results.length === 0) {
	dv.el("p", "No results to show")
} else {
	dv.table(["Notes", "Source"], results.map(r => ["[[" + r.name + "]]", r.frontmatter.Source]))
}

//TEORICAMENTE ESTO FORMATEA
dv.container.classList.add("cv-jobs");

```