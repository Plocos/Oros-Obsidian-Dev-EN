---
Related to: 
tags: 
Query: ""
---

Search: `INPUT[text(placeholder(File name)):Query]`

```dataviewjs
var query = dv.current().file.frontmatter.Query

var results = dv.current().file.inlinks.where(r => {
	//Extra el nombre del inlink a traves de su ruta
	var name = "" + r.path.substring(r.path.lastIndexOf("/") + 1, r.path.lastIndexOf("."))
	
	if(query != null){
		if(!name.contains(query)){
			return false
		}
		return true
	}
	return true
})

//Crea un array y lo rellena con el tipo de cada archivo encontrado usando el path
var typeOfResults = []

results.forEach(r => {
	if(r.path.contains("200 - Projects")){
		typeOfResults.push("Project")
	}else if(r.path.contains("310 - Categories")){
		typeOfResults.push("Category")
	}else if(r.path.contains("320 - Notes")){
		typeOfResults.push("Note")
	}
})

//Queda por añadir el numero de inlinks del inlink
if(results.length === 0) {
	dv.el("p", "No results to show")
} else {
	dv.table(["Name", "Type"], results.map((r, index) => [r, "Type: " + typeOfResults[index].toString()]))
}

```