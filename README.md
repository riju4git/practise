# practise

Notes

import javax.annotation.processing.AbstractProcessor;
import javax.annotation.processing.RoundEnvironment;
import javax.annotation.processing.SupportedAnnotationTypes;
import javax.annotation.processing.SupportedSourceVersion;
import javax.lang.model.SourceVersion;
import javax.lang.model.element.Element;
import javax.lang.model.element.ElementKind;
import javax.lang.model.element.Modifier;
import javax.lang.model.element.TypeElement;
import javax.tools.Diagnostic;
import java.util.HashSet;
import java.util.Set;

@SupportedAnnotationTypes("AutoGenerateGetters")
@SupportedSourceVersion(SourceVersion.RELEASE_8)
public class AutoGenerateGettersProcessor extends AbstractProcessor {
    @Override
    public boolean process(Set<? extends TypeElement> annotations, RoundEnvironment roundEnv) {
        for (TypeElement annotation : annotations) {
            Set<? extends Element> annotatedElements = roundEnv.getElementsAnnotatedWith(annotation);

            for (Element element : annotatedElements) {
                if (element.getKind() == ElementKind.CLASS) {
                    generateGetters((TypeElement) element);
                } else {
                    processingEnv.getMessager().printMessage(Diagnostic.Kind.ERROR,
                            "@AutoGenerateGetters can only be applied to classes", element);
                }
            }
        }
        return true;
    }

    private void generateGetters(TypeElement classElement) {
        String packageName = processingEnv.getElementUtils().getPackageOf(classElement).toString();
        String className = classElement.getSimpleName().toString();
        String getterClassName = className + "Getters";

        StringBuilder generatedCode = new StringBuilder();
        generatedCode.append("package ").append(packageName).append(";\n\n");
        generatedCode.append("public class ").append(getterClassName).append(" {\n");

        for (Element element : classElement.getEnclosedElements()) {
            if (element.getKind() == ElementKind.FIELD && !element.getModifiers().contains(Modifier.STATIC)) {
                String fieldName = element.getSimpleName().toString();
                String fieldType = element.asType().toString();

                generatedCode.append("\tpublic ").append(fieldType).append(" get").append(fieldName.substring(0, 1).toUpperCase()).append(fieldName.substring(1)).append("() {\n");
                generatedCode.append("\t\treturn this.").append(fieldName).append(";\n");
                generatedCode.append("\t}\n\n");
            }
        }

        generatedCode.append("}\n");

        // Printing the generated code (optional)
        System.out.println(generatedCode.toString());
    }



        private void generateGetter(TypeElement classElement, Element fieldElement) {
        String packageName = processingEnv.getElementUtils().getPackageOf(classElement).toString();
        String className = classElement.getSimpleName().toString();
        String fieldType = fieldElement.asType().toString();
        String fieldName = fieldElement.getSimpleName().toString();
        String getterMethodName = "get" + fieldName.substring(0, 1).toUpperCase() + fieldName.substring(1);

        StringBuilder generatedCode = new StringBuilder();
        generatedCode.append("package ").append(packageName).append(";\n\n");
        generatedCode.append("public class ").append(className).append(" {\n");
        generatedCode.append("\tpublic ").append(fieldType).append(" ").append(getterMethodName).append("() {\n");
        generatedCode.append("\t\treturn this.").append(fieldName).append(";\n");
        generatedCode.append("\t}\n");
        generatedCode.append("}\n");

        // Printing the generated code (optional)
        System.out.println(generatedCode.toString());
    }
}
