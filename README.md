# decorador

-- Recuperar las propiedaddes con reflexion
-- Crear un decorador con attribite para rebicar que las pk no esten repetidas

# Ejemplo
El linq y el foreach que le sigue es lo importante : 
public ActionResult ReadDocument(ModifyAdmissionModel model)
{
    if (model.FileProperty == null)
    {
        return Json(new { success = false, msg = "Se debe seleccionar un archivo" });
    }

    var doc = model.FileProperty.SheetToList<TTAdmissionUpdate>();

    // Verificar si existen códigos duplicados
    var codigosDuplicados = doc.GroupBy(x => x.CodAdmision)
                               .Where(g => g.Count() > 1)
                               .SelectMany(g => g)
                               //obtiene todos los elementos con el many
                               .ToList();

    foreach (var item in codigosDuplicados)
    {
        item.Errores = "Código de admisión duplicado.";
    }

    // Convertir FechaAdmision al formato correcto
    foreach (var item in doc)
    {
        if (item.FechaAdmision.HasValue)
        {
            item.FechaAdmision = FormatearFechaSmallDateTime(item.FechaAdmision.Value);
        }


    }



    model.AdmissionSheet = doc;

    return PartialView("_AdmisionTable", model);
}
