package com.MinTIC.Controller;

import java.util.HashMap;
import java.util.List;
import java.util.Map;
import java.util.stream.Collectors;

import javax.validation.Valid;

import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.dao.DataAccessException;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.PutMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

import com.MinTIC.Estudiantes.Estudiantes;
import com.MinTIC.services.EstudianteService;

@RestController
@RequestMapping("/api")
public class EstudiantesRestController {
	
	@Autowired
	private EstudianteService estudianteservice;
	
	@GetMapping("/estudiantes")
	public List<Estudiantes> index(){
		return estudianteservice.findAll();
	}
	
	@GetMapping("/estudiantes/(id)")
	public Estudiantes show(@PathVariable Long id) {
		
		return estudianteservice.findById(id);
	}
	
	@PostMapping("/estudiantes")
	public ResponseEntity <?> create(@Valid @RequestBody Estudiantes estudiante,BindingResult result) {
		
		Estudiantes estudiantesNew =null;
		
		
		Map<String,Object> response=new HashMap<>();
		
		if (result.hasErrors()) {
			List<String> errors=result.getFieldErrors()
					.stream()
					.map(err-> "El Campo" +err.getField()+""+err.getDefaultMessage())
					.collect(Collectors.toList());
			response.put("Errors", errors);
			return new ResponseEntity<Map<String,Object>>(response,HttpStatus.BAD_REQUEST);
		
		}
		try {
			estudiantesNew=this.estudianteservice.save(estudiante);
			
			
			
		}catch(DataAccessException e) {
			
			response.put("mensaje", "Error al realizar el insert en la base de datos");
			response.put("error", e.getMessage().concat(":").concat(e.getMostSpecificCause().getMessage()));
			return new ResponseEntity<Map<String,Object>>(response,HttpStatus.INTERNAL_SERVER_ERROR);
		}
		
		response.put("mensaje", "El estudiante se creo exitosamente");
		response.put("Estudiante", estudiantesNew);
		return new ResponseEntity<Map<String,Object>>(response,HttpStatus.CREATED);
		

	}
	
	/* Metodo para Actualizar*/
	
	@PutMapping("/estudiantes/{id}")
	public ResponseEntity <?> update(@Valid @RequestBody Estudiantes estudiante,BindingResult result,@PathVariable Long id) {
		
		Estudiantes currentEstudiante=this.estudianteservice.findById(id);
		
		Estudiantes updateEstudiante = null;
		

		Map<String,Object> response=new HashMap<>();
		
		if (result.hasErrors()) {
			List<String> errors=result.getFieldErrors()
					.stream()
					.map(err-> "El Campo" +err.getField()+""+err.getDefaultMessage())
					.collect(Collectors.toList());
			response.put("Errors", errors);
			return new ResponseEntity<Map<String,Object>>(response,HttpStatus.BAD_REQUEST);
		
		}
		
		if(currentEstudiante==null) {
			response.put("Mensaje", "Error: No se puede editar el estudiante solicitado:".concat(id.toString())
					.concat("No existe en la base de datos"));
			return new ResponseEntity<Map<String,Object>>(response,HttpStatus.NOT_FOUND);
		}
		try {
			
			currentEstudiante.setNombre(estudiante.getNombre());
			
			updateEstudiante=this.estudianteservice.save(currentEstudiante);
			
			
			
			
		}catch(DataAccessException e) {
			
			response.put("mensaje", "Error al realizar el insert en la base de datos");
			response.put("error", e.getMessage().concat(":").concat(e.getMostSpecificCause().getMessage()));
			return new ResponseEntity<Map<String,Object>>(response,HttpStatus.INTERNAL_SERVER_ERROR);
		}
		
		/*response.put("mensaje", "El estudiante se creo exitosamente");
		response.put("Estudiante", estudiantesNew);
		return new ResponseEntity<Map<String,Object>>(response,HttpStatus.CREATED);*/
		

	}
	
	

}
