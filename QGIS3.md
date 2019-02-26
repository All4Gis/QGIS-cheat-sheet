
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


__How many algorithms are there?__

	len(QgsApplication.processingRegistry().algorithms())

TOC
---

__Access checked Layers__

	iface.mapCanvas().layers()

__Obtain Layers name__

	canvas = iface.mapCanvas()
	layers = [canvas.layer(i) for i in range(canvas.layerCount())]
	layers_names = [ layer.name() for layer in layers ]
	print "layers TOC = ", layers_names
	
	or
	
	layers = [layer for layer in QgsProject.instance().mapLayers().values()]


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

__See the CRS__

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

Layers
---

__Add Vector layer__

	layer = iface.addVectorLayer("/path/to/shapefile/file.shp", "layer name you like", "ogr")


__Hide a field column__

	def fieldVisibility (layer,fname):
	  setup = QgsEditorWidgetSetup('Hidden', {})
	  for i, column in enumerate(layer.fields()):
	    if column.name()==fname:
	      layer.setEditorWidgetSetup(idx, setup)
		break
	    else:
	      continue
	      
__Move geometry__      

	geom = feat.geometry()
	geom.translate(100, 100)
	feat.setGeometry(geom)

__Adding new feature__

	iface.openFeatureForm(iface.activeLayer(), QgsFeature(), False)


Settings
---

__Get QSettings list__

	from PyQt5.QtCore import QSettings
	qs = QSettings()

	for k in sorted(qs.allKeys()):
	    print (k)



Common PyQGIS functions
---

https://github.com/boundlessgeo/lib-qgis-commons

https://github.com/inasafe/inasafe/tree/master/safe/utilities

https://docs.qgis.org/testing/en/docs/pyqgis_developer_cookbook/index.html

https://pcjericks.github.io/py-gdalogr-cookbook/index.html

http://www.green-forums.info/greenlib/geolibrary/Lawhead%20J/QGIS%20Python%20Programming%20Cookbook.%2020%20%2852%29/QGIS%20Python%20Programming%20Cookboo%20-%20Lawhead%20J.pdf (a commercial 2015 book, but seems to have been released for download)


Sources
---

<http://docs.qgis.org/testing/en/docs/pyqgis_developer_cookbook/>

<http://qgis.org/api/>

<https://stackoverflow.com/questions/tagged/qgis>

