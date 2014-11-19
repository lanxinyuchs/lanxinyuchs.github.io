---
layout: page
---

<ul class="listing">
	<span><a title="C_Language" class="" href="/Tech/js_test.html">C/C++</a></span>
	<span><a title="Embedded" class="" href="/Embedded/">Embedded</a></span>
	<span><a title="ARM" class="" href="/ARM/">ARM</a></span>
	<span><a title="Tools" class="" href="/Tools/">Tools</a></span>
	<span><a title="RTOS" class="" href="/RTOS/">RTOS</a></span>
	<span><a title="Linux" class="" href="/Linux/">Linux</a></span>
</ul>

<script src="/media/js/jquery.tagcloud.js" type="text/javascript"></script> 

<script language="javascript">
$.fn.tagcloud.defaults = {
    size: {start: 1, end: 1, unit: 'em'},
      color: {start: '#f8e0e6', end: '#ff3333'}
};
$(function () {
    $('#tag_cloud a').tagcloud();
});
</script>
