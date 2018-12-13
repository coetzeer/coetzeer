---
layout: post
title: "Attributed Images"
img: gallery.jpg
tags: [images] # add tag
toc: True
---

<table>
<thead>
    <tr>
        <td>#</td>
        <td>Filename</td>
        <td>Copyright</td>
        <td>License</td>
        <td>Link</td>
    </tr>
</thead>
    <tbody>
    <!--{% increment imagenumber %}-->
    {% assign list = site.data.attributable_images | sort %}
    {% for img in list %}
    {% assign filename = img[0] %}
    
      <tr>
        <td><a href="{{filename}}"></a>{% increment imagenumber %}</td>
        <td>
            <a href="{{img[1].source}}" >{{filename}}</a>
         </td>
        <td>
            {{img[1].copyright | default : 'Unknown'}}
         </td>
         <td>
            {{img[1].license | default : 'Unknown'}}
         </td>  
        <td>
        
        {% if img[1].link %}
            <a href="{{img[1].link}}">{{img[1].link}}</a>
        {% else %}
            Unknown
        {% endif %}
        
         </td>  
      </tr>
    {% endfor %}
    </tbody>
</table>
