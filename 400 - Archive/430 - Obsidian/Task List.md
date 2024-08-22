---
cssclasses: 
Proyecto: ""
Tag: ""
Fecha: ""
Activo: false
---

### Tasks list
#### Filters
Project: `INPUT[text(placeholder(Project name)):Proyecto]`
Tag: `INPUT[text(placeholder(Starts with #)):Tag]`
Date: `INPUT[date:Date]`
Only active: `INPUT[toggle(defaultValue(false),class(marginTop)):Activo]`

```dataviewjs
var proyecto = dv.current().file.frontmatter.Proyecto
var tag = dv.current().file.frontmatter.Tag
var fechaTemp = dv.current().file.frontmatter.Fecha
var fecha = fechaTemp.substring(8) + "-" + fechaTemp.substring(5, 8) + fechaTemp.substring(0, 4)
var activo = dv.current().file.frontmatter.Activo

var tareas = dv.pages('"100 - Inbox"').file.tasks

//tareas de proyectos
var trabajos = dv.pages('-"100 - Inbox" and -"200 - Projects/+ Projects Kanban" and -"400 - Archive"').file.tasks
.where(t => {
	if(t.text.contains("```")){
		return false
	}
	//Descarta la tarea si ya esta completada
	if(t.completed == true){
		return false
	}
	if(tag != null){
		if(!t.text.contains(tag)){
			return false
		}
	}
	return true
})
.sort(t => dv.date(t.due))

var results = tareas.concat(trabajos)

dv.header(3, "Results")
if(results.length === 0) {
	dv.el("p", "No pending tasks")
} else {
	var lastFile = ""
	var lastState = ""
	
	results.forEach(r => {
	//Recupera el archivo de cada una de las tareas para agruparlas y mostrarlo
	var file = "[[" + r.section.toString().substring(r.section.toString().indexOf("|") + 1, r.section.toString().indexOf(">") - 1) + "]]"
	
	//Recupera el estado de cada una de las tareas para agruparlas y mostrarlo
	var state = r.section.toString().substring(r.section.toString().lastIndexOf(">") + 2, r.section.toString().lastIndexOf("]]"))
	
	//Descarta la tarea si ya esta en la columna hecho
	if(state.contains("Done")){
		return false;
	}
	
	//Descarta la tarea si no pertenece al proyecto especificado
	if(proyecto != null && proyecto != "" && !file.toString().toLowerCase().contains(proyecto.toLowerCase())){
		return false
	}
	
	if(fecha != "-"){
		var checkFecha = false
		r.outlinks.forEach(o => {
			if(o.toString().contains("-20")){
				if(o.toString().contains(fecha)){
					checkFecha = true
					return
				}
			}			
		})
		if(checkFecha == false){
			return false
		}
	}
	
	if(activo == true){
		if(!state.contains("Active")){
			return false
		}
	}
	
	//Printeamos y cambiamos de file y refrescamos estados
	if(file != lastFile){
		dv.header(4, file)
		lastState = ""
	}
	lastFile = file

	//Printeamos y cambiamos de estado
	if(state != lastState){
		dv.header(5, state)
	}
	lastState = state
	
	dv.el("p", "- " + r.text)
})
}
```