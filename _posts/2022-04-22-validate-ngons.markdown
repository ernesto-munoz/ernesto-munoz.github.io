---
layout: post
title:  "Validate NGons!"
date:   2022-04-22 16:45:39 +0200
categories: jekyll update
---
Check the NGons in all the meshes in the scene

{% highlight python %}
def validate_ngons(self):
  """ Validate that there are not any object with ngons

  Return example:
  {
      'objects_with_ngons': {
          "|pro_pipeTest_mdl|geo_pipeMain_n_00": [752, 753]
      },
      'result': False
  }

  :return:
  """
  objects_with_ngons = dict()
  for each_mesh in cmds.ls(exactType="mesh", long=True):
      faces_num = cmds.polyEvaluate(each_mesh, face=True)
      for face_id in range(faces_num):
          vertex = cmds.polyListComponentConversion("{}.f[{}]".format(each_mesh, face_id), fromFace=True, toVertex=True)
          polygon_vertices = cmds.filterExpand(vertex, selectionMask=31)
          if polygon_vertices is not None:
              if len(polygon_vertices) > 4:
                  invalid_object = cmds.listRelatives(each_mesh, parent=True, fullPath=True)[0]
                  if invalid_object not in objects_with_ngons.keys():
                      objects_with_ngons[invalid_object] = list()
                  objects_with_ngons[invalid_object].append(face_id)

  is_valid = len(objects_with_ngons) == 0

  return {
      "result": is_valid,
      "objects_with_ngons": objects_with_ngons
  }
{% endhighlight %}
