---
layout: post
published: true
title: auto-value-variant
---
## An AutoValue Extension


````
private static Set<String> getAnnotations(ExecutableElement element) {
	Set<String> set = new LinkedHashSet<>();

	List<? extends AnnotationMirror> annotations = element.getAnnotationMirrors();
	for (AnnotationMirror annotation : annotations) {
		set.add(annotation.getAnnotationType().asElement().getSimpleName().toString());
	}

	return Collections.unmodifiableSet(set);
}
````

Enter text in [Markdown](http://daringfireball.net/projects/markdown/). Use the toolbar above, or click the **?** button for formatting help.
