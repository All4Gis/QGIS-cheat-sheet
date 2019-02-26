
# QGIS 3 cheat sheet

Cheat sheet for PyQgis

Canvas
---
__Access Canvas__

	canvas = iface.mapCanvas()


Processing algorithms 
---

__Get algorithms list__

	for alg in QgsApplication.processingRegistry().algorithms():
		print("{}:{} --> {}".format(alg.provider().name(), alg.name(), alg.displayName()))
		
	def alglist():
	  s = ''
	  for i in QgsApplication.processingRegistry().algorithms():
	    l = i.displayName().ljust(50, "-")
	    r = i.id()
	    s += '{}--->{}\n'.format(l, r)
	  print(s)

	alglist()

__Get algorithms Help__

Random selection

	import processing
	processing.algorithmHelp("qgis:randomselection")


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

__Find layer by name__

	from qgis.core import QgsProject
	layer = QgsProject.instance().mapLayersByName("name")[0]
	print layer.name()

__Set Active layer__

	from qgis.core import QgsProject
	layer = QgsProject.instance().mapLayersByName("name")[0]
	iface.setActiveLayer(layer)

__Remove all layers__

	QgsProject.instance().removeAllMapLayers()

__see the CRS__

	for layer in QgsProject().instance().mapLayers().values():   
	    crs = layer.crs().authid()
	    layer.setName(layer.name() + ' (' + crs + ')')

__Load all layers from GeoPackage__

	fileName = "sample.gpkg"
	layer = QgsVectorLayer(fileName,"test","ogr")
	subLayers =layer.dataProvider().subLayers()

	for subLayer in subLayers:
		name = subLayer.split('!!::!!')[1]
		uri = "%s|layername=%s" % (fileName, name,)
		#Create layer
		sub_vlayer = QgsVectorLayer(uri, name, 'ogr')
		#Add layer to map
		QgsProject.instance().addMapLayer(sub_vlayer)
		
Settings
---

__Get QSettings list__

	from PyQt5.QtCore import QSettings
	qs = QSettings()

	for k in sorted(qs.allKeys()):
	    print (k)

