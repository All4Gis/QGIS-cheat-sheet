# QGIS cheat sheet

Cheat sheet for PyQgis

Canvas
---


TOC
---

__Access checked Layers__

	iface.mapCanvas().layers()

__Obtain Layers name__

	canvas = iface.mapCanvas()
	layers = [canvas.layer(i) for i in range(canvas.layerCount())]
	layers_names = [ layer.name() for layer in layers ]
	print "layers TOC = ", layers_names

__Add vector layer__

	layer = iface.addVectorLayer("input.shp", "name", "ogr")
	if not layer:
	  print "Layer failed to load!"




Processing algorithms 
---

__Get algorithms list__

	import processing
	processing.alglist()

__Get algorithms Help__

Random selection

	processing.alghelp('qgis:randomselection')


__Get algorithms Options__

Random selection

	processing.algoptions('qgis:randomselection')





Sources
---

<http://docs.qgis.org/testing/en/docs/pyqgis_developer_cookbook/>

<http://qgis.org/api/>

<https://stackoverflow.com/questions/tagged/qgis>

<https://gis.stackexchange.com/questions/tagged/pyqgis>




