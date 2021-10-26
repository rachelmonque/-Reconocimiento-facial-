-Reconocimiento-facial-con-multiprocesamiento-
   
El siguiente codigo tiene la funcionalidad de reconocer caracteristicas faciales mediante imagenes y esta progrmado en python(localmente) se necesita un repositorio de microsoft azure e importar las siguientes librerias:
 
from multiprocessing import Pool

import concurrent.futures

from timeit import default_timer

import numpy as np

from matplotlib import pyplot as plt

import sys

import requests

import json

import cognitive_face as CF

from PIL import Image, ImageDraw, ImageFont

import cv2

Para utilizar la herrramienta de microsoft azure solamente se debe crear un repositorio y posteriormente ingresar un KEY y un URL que le genera su API.
Ejemplo:

SUBSCRIPTION_KEY = 'bdd40bc3zb2a33h998jn0jl34x85ui9ek2' #Ingrese su KEY

BASE_URL = 'https://alejandra1.cognitiveservices.azure.com/face/v1.0/' #Aca va su dirección
CF.BaseUrl.set(BASE_URL)

CF.Key.set(SUBSCRIPTION_KEY)

A continuacion se muestra el código:

   La siguiente lista vacia lo que hace es guardar la cantidad de veces que se ejecuta un ejercicio.
   
    history_list = []


   La siguiente funcion lo que hace es mostrar un rectangulo en el rostro de la persona.

    def show_image(path, faces):
    
    image = Image.open(path)
    draw = ImageDraw.Draw(image)
    for faceRectangle in faces:
        top = faceRectangle['top']
        left = faceRectangle['left']
        width= faceRectangle['width']
        height = faceRectangle['height']
        draw.rectangle((left,top, left+width, top+height), outline='red', width=2)

    image.show()
    
   A continuacion se muestran 2 diferentes ordenamientos para listas que seran utilizados más adelante:
   
   El primer ordenamiento es el bubble este lo que hace es ordenar las listas con sublistas en su posición descrita por un rango de 10
   
    def bubble_ordering(list1):
       
        '''Arguments: 
        list {list} -- Lista con sublistas que contienen la edad y el sexo
        Return: list1 -- Devuelve la lista con sublistas que contienen edades y géneros ordenados en un rango de 10'''
        
        past = len(list1) - 1
        while past > 0:
          pos = 0
          while pos < past:
             if list1[pos][0] > list1[pos+1][0]:
                temp = list1[pos]
                list1[pos] = list1[pos+1]
                list1[pos+1] = temp
             pos = pos + 1
          past = past - 1
        return list1
    
  La siguiente funcion divide la lista en sublistas
    
    def list_partition(l):
    '''Return: 
       [menores,pivote,mayor] -- Devuelve la lista con sublistas'''
    
        minors=list()
        greater=list()
        pivot=l[-1]
        for x in l[:-1]:
            if x<pivot:
                minors.append(x)
            else:
                greater.append(x) 
        return([minors,pivot,greater])
   
   El segundo ordenamiento es el quicksort, más adelante lo que hace es ordenar los géneros y las edades de las caras en una lista con sublistas
   
    def quickSort_ordering(list1):
    '''Arguments: 
    list{list} -- Lista con sublistas que contienen géneros y edades
    Return: 
    list1 - Devuelve la lista con sublistas que contienen géneros y edades'''
      
         if len(list1)>0:
         list1=list_partition(list1)
         cont=0
         while cont<len(list1):
             e=list1[cont]
             if type(e)==list:
                 if len(e)==0:
                    del list1[cont]
                 elif len(e)==1:
                    list1[cont]=e[0]
                    cont = cont + 1
                 else:
                    list1=list1[:cont]+list_partition(e)+list1[cont+1:]
             else:
                 cont=cont+1
        return(list1)
    
   La siguiente funcion obtiene las caracteristicas de las personas de una imagen.
   
    def emotions(picture)
    '''Arguments: picture {string} -- es la imagen
   
       Return: 
       una lista con información sobre las personas'''
   
        image_path = picture

        image_data = open(image_path, "rb").read()
        headers = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY,
        'Content-Type': 'application/octet-stream'}
        params = {
         'returnFaceId': 'true',
         'returnFaceLandmarks': 'false',
         'returnFaceAttributes': 'age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise',
        }
        response = requests.post(
                             BASE_URL + "detect/", headers=headers, params=params, data=image_data)
        analysis = response.json()
        return analysis 

   El siguiente fragmento de código permite mostrar una lista con información sobre las personas
    
    def point3(picture):
    '''Arguments: 
       picture {string} -- es la imagen'''  
  
    image_path = picture
    
        image_data = open(image_path, "rb").read()
        headers = {'Ocp-Apim-Subscription-Key': SUBSCRIPTION_KEY,
        'Content-Type': 'application/octet-stream'}
        params = {
         'returnFaceId': 'true',
         'returnFaceLandmarks': 'false',
         'returnFaceAttributes': 'age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise',
        }
        response = requests.post(
                             BASE_URL + "detect/", headers=headers, params=params, data=image_data)
        analysis = response.json() 

        print(analysis)

   La siguiente función indica el número de caras en la imagen y la edad asociada a cada cara.
   
    def a(analysis):
    '''Arguments: 
    analysis {list} -- es una lista con la información de las personas'''

        print(" \nconsultation 'a' \n")
        print ("number of faces: ", len(analysis))
        for x in analysis:
         faceId= x["faceId"]
         faceAttributes= x["faceAttributes"]
         age= faceAttributes["age"]
        
         str_out= faceId, " has an age of: ",age

         print(faceId, " has an age of: ",age)
         str_out_history= ("Option 'a' was executed and the result is: ", str_out)
         history_list.append(str_out_history)

   La siguiente funcion Muestra una lista con todas las edades de las personas e indica quién es la persona de mayor edad.
    
    def b(analysis):
    '''Arguments: 
       analysis {list} -- es una lista con la información de las personas'''
  
        print('\nConsultation b\n')
        ages = []
        for x in analysis: 
        faceAttributes = x["faceAttributes"]
         age = faceAttributes["age"]
         ages.append(age)

        str_out = "Ages =", ages," and the oldest has ", max(ages)
        print("Ages =", ages," and the oldest has ", max(ages))
        str_out_history = ('Option b was executed and the result is: ', str_out)
        history_list.append(str_out_history)

   La siguiente funcion ordena las sublistas de una lista en un rango de 10 
    
    def sublistRange10(list_ages_genders):
    '''Arguments:
    list {list} -- Lista con sublistas que contienen edades y géneros
    Return: list_ages_genders -- Devuelve la lista con sublistas que contienen las edades y los géneros con un rango de 10'''
    
        cont = 0
        ages_zero = []
        ages_ten = []
        ages_twenty = []
        ages_thirty = []
        ages_fourty = []
        ages_fifty = []
        ages_sixty = []
        ages_seventy = []
        ages_eigthy = []
        ages_ninety = []
        while cont < len(list_ages_genders):
         aux = []
         if list_ages_genders[cont][0] >= 0 and list_ages_genders[cont][0] <=9:
            aux.append(list_ages_genders[cont][0])
            aux.append(list_ages_genders[cont][1])
            ages_zero.append(aux)
         if list_ages_genders[cont][0] >= 10 and list_ages_genders[cont][0] <=19:
            aux.append(list_ages_genders[cont][0])
            aux.append(list_ages_genders[cont][1])
            ages_ten.append(aux)
         if list_ages_genders[cont][0] >= 20 and list_ages_genders[cont][0] <=29:
            aux.append(list_ages_genders[cont][0])
            aux.append(list_ages_genders[cont][1])
            ages_twenty.append(aux)
         if list_ages_genders[cont][0] >= 30 and list_ages_genders[cont][0] <=39:
            aux.append(list_ages_genders[cont][0])
            aux.append(list_ages_genders[cont][1])
            ages_thirty.append(aux)
         if list_ages_genders[cont][0] >= 40 and list_ages_genders[cont][0] <=49:
            aux.append(list_ages_genders[cont][0])
            aux.append(list_ages_genders[cont][1])
            ages_fourty.append(aux)
         if list_ages_genders[cont][0] >= 50 and list_ages_genders[cont][0] <=59:
            aux.append(list_ages_genders[cont][0])
            aux.append(list_ages_genders[cont][1])
            ages_fifty.append(aux)
         if list_ages_genders[cont][0] >= 60 and list_ages_genders[cont][0] <=69:
            aux.append(list_ages_genders[cont][0])
            aux.append(list_ages_genders[cont][1])
            ages_sixty.append(aux)
         if list_ages_genders[cont][0] >= 70 and list_ages_genders[cont][0] <=79:
            aux.append(list_ages_genders[cont][0])
            aux.append(list_ages_genders[cont][1])
            ages_seventy.append(aux)
         if list_ages_genders[cont][0] >= 80 and list_ages_genders[cont][0] <=89:
            aux.append(list_ages_genders[cont][0])
            aux.append(list_ages_genders[cont][1])
            ages_eigthy.append(aux)
         if list_ages_genders[cont][0] >= 90 and list_ages_genders[cont][0] <=99:
            aux.append(list_ages_genders[cont][0])
            aux.append(list_ages_genders[cont][1])
            ages_ninety.append(aux)
         cont = cont + 1
        list_ages_genders = []
        if len(ages_zero) > 0:
         list_ages_genders.append(ages_zero)
        if len(ages_ten) > 0:
         list_ages_genders.append(ages_ten)
        if len(ages_twenty) > 0:
         list_ages_genders.append(ages_twenty)
        if len(ages_thirty):
         list_ages_genders.append(ages_thirty)
        if len(ages_fourty) > 0:
         list_ages_genders.append(ages_fourty)
        if len(ages_fifty) > 0:
         list_ages_genders.append(ages_fifty)
        if len(ages_sixty) > 0:
         list_ages_genders.append(ages_sixty)
        if len(ages_seventy) > 0:
         list_ages_genders.append(ages_seventy)
        if len(ages_eigthy) > 0:
         list_ages_genders.append(ages_eigthy)
        if len(ages_ninety):
         list_ages_genders.append(ages_ninety)
        return list_ages_genders

   Crea una lista con sublistas para mostrar el faceId, la edad y el sexo de las caras presentes en las imágenes.
   
   Ordena por edad utilizando el método de ordenación por inserción.
    
    def c(analysis):
    '''Arguments: 
       analysis {lista} -- es una lista con la información de las personas'''
    

        print("\n consultation 'c'\n ")
        lista=[]
        for x in analysis:
         faceId= x["faceId"] 
         faceAttributes= x["faceAttributes"]
         age= faceAttributes["age"]
         gender= faceAttributes["gender"]
         sublista=[]
         sublista.append(faceId)
         sublista.append(age)
         sublista.append(gender)
         lista.append(sublista)
        newlist= ordering(lista)
        str_out= newlist
        print(newlist)
        str_out_history= ("Option 'c' was executed and the result is: ", str_out)
        history_list.append(str_out_history)

   La siguiente funcion extrae los datos más altos de la lista
   
    def higher(lista):
    '''Arguments: 
       lista {list}-- lista con sublista que contiene el faceId, la edad y el género 
   
       Return: higher -- es la sublista con la edad más alta'''
  
       if len(lista)==0:
         return None 
       else:
         higher= lista[0]
         for x in lista[1:]:
            if x[1]>higher[1]:
                higher=x
         return higher 
    
   La siguiente funcion ordena la lista con sublistas de menor a mayor
    
    def ordering(lista):
    '''Arguments: 
       lista {list} -- lista con sublistas que contienen el faceId, la edad y el género
   
       Return: newlist -- es la nueva lista con sublistas ordenadas de menor a mayor'''
   
        newlist=[]
        while len(lista)>0:
         Higher= higher(lista)
         newlist.insert(0,Higher)
         lista.remove(Higher)
        return newlist 

   La siguiente funcion crea una lista con sublistas para mostrar la edad y el sexo de las caras en las imágenes.
   
   Las listas están en un rango de 10 y se ordenan utilizando el método de ordenación burbuja.
   
  
    def d(analysis):
    '''Arguments: 
       analysis {list} -- es una lista con la información de las personas'''


        print('\nConsultation d\n')
        ages_genders = []
        for x in analysis:
         aux = []
         faceAttributes = x["faceAttributes"]
         age = faceAttributes["age"]
         gender = faceAttributes["gender"]
         aux.append(age)
         aux.append(gender)
         ages_genders.append(aux)
       ages_genders = bubble_ordering(ages_genders) 
       str_out = sublistRange10(ages_genders)
    
       print(sublistRange10(ages_genders))
       str_out_history = ('Option d was executed and the result is: ', str_out)
       history_list.append(str_out_history)

   La siguiente funcion muestra la edad de todas las personas presentes en una imagen en
   una lista y finalmente el faceId y la edad de la(s) persona(s) que se
   más cercana a la edad media con un rango de + - 5 años.
   
    def e(analysis):
    '''Arguments: 
       analysis {list} -- es una lista con la información de las personas'''
   
        print('\nConsultation e\n')
        ages_faceIds = []
        for x in analysis:
         aux = []
         faceAttributes = x["faceAttributes"]
         age = faceAttributes["age"]
         faceId = x["faceId"]
         aux.append(age)
         aux.append(faceId)
         ages_faceIds.append(aux)

        average = 0
        sumatory = 0.0
        cont = 0
        while cont < len(ages_faceIds):
         sumatory = sumatory + ages_faceIds[cont][0]
         cont = cont+1
        average = sumatory/cont
        less_five_age = average - 5
        plus_five_age = average + 5

        ages_average = []
        cont = 0
        while cont < len(ages_faceIds):
         if ages_faceIds[cont][0] >= less_five_age and ages_faceIds[cont][0] <= plus_five_age:
            ages_average.append(ages_faceIds[cont])
         cont = cont+1
        cont = 0
        ages = []
        while cont < len(ages_faceIds):
         ages.append(ages_faceIds[cont][0])
         cont = cont+1
       if ages_average==[]:
        print('Ages=', ages ,' and there are not people with a range of +-5 years' )
       else:
        str_out = 'Ages=', ages ,' and the people closest to the average age (the average age is ', average, ' ) with a range of +-5 years are: ', ages_average
        print('Ages=', ages ,' and the people closest to the average age (the average age is ', average, ' ) with a range of +-5 years are: ', ages_average)
        str_out_history = ('Option e was executed and the result is: ', str_out)
        history_list.append(str_out_history)

   La siguiente funcion crea una lista con sublistas que muestran la edad y el género de las caras en las imágenes.
   
   Tambien muestra las personas separadas por género en sublistas, el género masculino va primero, y las sublistas 
   se ordenan utilizando el método de ordenación quicksort.
   
    def f(analysis):
    '''Arguments: 
        análisis {lista} -- es una lista con la información de las personas''' 
   
        print('\nConsultation f\n')
        ages_genders = []
        ages_male = []
        ages_female = []
        for x in analysis:
         faceAttributes = x["faceAttributes"]
         age = faceAttributes["age"]
         gender = faceAttributes["gender"]
         if gender == "male":
           ages_male.append(age)
         else:
           ages_female.append(age)


        ages_male = quickSort_ordering(ages_male)
        ages_female = quickSort_ordering(ages_female)

        ages_genders_male = []
        ages_genders_female = []
        ages_genders_male_female = []
        aux = []
        for x in ages_male:
         aux = []
         aux.append(x)
         aux.append('male')
         ages_genders_male.append(aux)
        for x in ages_female:
         aux = []
         aux.append(x)
         aux.append('female')
         ages_genders_female.append(aux)

        ages_genders_male_female.append(ages_genders_male)
        ages_genders_male_female.append(ages_genders_female)
        str_out = ages_genders_male_female
        print(ages_genders_male_female)
        str_out_history = ('Option f was executed and the result is: ', str_out)
        history_list.append(str_out_history)
 
   La siguiente funcion crea una lista con sublistas para mostrar el faceId, la edad y el sexo de las caras presentes en las imágenes. 
   
   Se ordena por edad utilizando el método de ordenación por inserción.

    def g(analysis):
    '''Argumentos: 
       analysis {list} -- es una lista con la información de las personas''' 

        print(" \nConsultation 'g'\n ")
        lista=[]
        for x in analysis:
         faceId= x["faceId"]
         faceAttributes= x["faceAttributes"]
         age= faceAttributes["age"]
         gender= faceAttributes["gender"]
         sublista=[]
         sublista.append(faceId)
         sublista.append(age)
         sublista.append(gender)
         lista.append(sublista)

        str_out= ordering(lista)
        print( str_out)
        str_out_history= ("Option 'g' was executed and the result is: ", str_out)
        history_list.append(str_out_history)

  La siguiente funcion muestra el faceId y el color de pelo más predominante de cada persona.
   
    def h(analysis):
     '''Arguments:
        analysis {list} -- es una lista con la información de las personas'''

        print("\n Consultation 'h' \n")
        for x in analysis:
         lista=[]
         faceId= x["faceId"]
         faceAttributes= x["faceAttributes"]
         hair= faceAttributes["hair"]
         hairColor= hair["hairColor"]
         for x in hairColor:
            color= x["color"]
            confidence= x["confidence"]
            lista1=[]
            lista1.append(color)
            lista1.append(confidence)
            lista1.append(faceId)
            lista.append(lista1)
        
          if lista==[]:
            lista.append(faceId)
            str_out= "faceId: ", faceId, " doesn't hair "
            print("faceId: ", faceId, " doesn't hair ")
            str_out_history= ("Option 'h' was executed and the result is: ", str_out)
            history_list.append(str_out_history)
          else: 
            p= higher(lista)
            str_out="faceId: ", p[2]," predominant hair color: ", p[0]
            print("faceId: ", p[2]," predominant hair color: ", p[0])
            str_out_history= ("Option 'h' was executed and the result is: ", str_out)
            history_list.append(str_out_history)
   
   La siguiente funcion muestra la caraId y la emoción más predominante de una persona.
   
    def i(analysis):
    '''Arguments: 
        analysis {list} -- es una lista con la información de las personas'''
    
        print('\nConsultation i\n')
        emotions_person = []
        predominant_emotion = []
        faceId = ""
        for x in analysis:
         faceAttributes = x["faceAttributes"]
         emotions_person = faceAttributes["emotion"]
         faceId = x["faceId"]
        higherValue = 0
        for x in emotions_person:
         aux = emotions_person[x]
         if aux > higherValue:
            higherValue = aux
            predominant_emotion = x
        
        str_out = ('FaceId: ', faceId,' Predominant emotion: ', predominant_emotion)
        print('FaceId: ', faceId,' Predominant emotion: ', predominant_emotion)
        str_out_history = ('Option i was executed and the result is: ', str_out)
        history_list.append(str_out_history)

  La siguiente funcion muestra los accesorios que lleva una persona.
 
    def j(analysis):
     '''Argumentos:
     picture {string} -- es la imagen
     analysis:{list} -- es una lista con la información de las personas'''
   
        print('\nConsultation j\n')
        accessories_type = []
        for x in analysis:
         faceAttributes = x["faceAttributes"]
         accessories = faceAttributes["accessories"]
        for x in accessories:
         accessories_type.append(x['type'])
        if accessories_type==[]:
         print("the person don't have accessories")
         str_out = ("the person don't have accessories")  
         str_out_history = ('Option j was executed and the result is: ', str_out)
         history_list.append(str_out_history)
        else:
          print('Accessories of a person in the image:')
          for x in accessories_type:
            print (x)
            str_out = ('Accessories of a person in the image: ', x)  
            str_out_history = ('Option j was executed and the result is: ', str_out)
            history_list.append(str_out_history)
 
   La siguiente funcion muestra la imagen con un rectángulo en las caras
   
    def k(analysis,picture):
     '''Arguments:
     picture {string} -- es la imagen
     analysis {list} -- es una lista con la información de las personas'''  
    
        print(" \nConsultation 'k'\n ")

        faces = []
        for face in analysis:
         faceRectangle = face['faceRectangle']
         faces.append(faceRectangle)
        show_image(picture,faces)

        str_out_history= ("Option k was executed and the result was to show image with a rectangle in the faces")
        history_list.append(str_out_history)

   La siguiente funcion muestra la persona que tiene la media de felicidad más alta pero menor que la media de edad de las personas 
   
    def l(analysis):
    '''Arguments: 
    analysis {list} -- es una lista con la información de las personas'''

        print(" \nConsultation 'l'\n ")
        suma_age=0
        cont=0
        prom=0
        lista=[]
        for x in analysis:
         faceId= x["faceId"]
         faceAttributes= x["faceAttributes"]
         age= faceAttributes["age"]
         suma_age=suma_age+ age
         cont=cont+1
         emotion=faceAttributes["emotion"]
         happiness= emotion["happiness"]
         lista1=[]
         lista1.append(faceId)
         lista1.append(happiness)
         lista.append(lista1)

        prom=prom+suma_age//cont 
        y= higher(lista)


        if y[1]< prom:
         str_out="the face with the highest percentage of happiness  and lower than the average age is: ", y[0], " with a average of: ", y[1]
         print("the face with the highest percentage of happiness  and lower than the average age is: ", y[0], " with a average of: ", y[1])
         str_out_history= ("Option 'l' was executed and the result is: ", str_out)
         history_list.append(str_out_history)
        
        else:
         str_out= "the highest happiness percentage is not below the average age of the people in the photo."
         print("the highest happiness percentage is not below the average age of the people in the photo.")
         str_out_history= ("Option 'l' was executed and the result is: ", str_out)
         history_list.append(str_out_history)

   La siguiente funcion muestra a la persona que tiene la media más alta de felicidad y que está por debajo de la media de edad de las personas de la foto.
  
    def m(analysis):
     ''''Arguments: 
     analysis {list} -- es una lista con la información de las personas'''  

        print(" \nConsultation 'm'\n ")
        suma_age=0
        cont=0
        prom=0
        lista=[]
        for x in analysis:
         faceId=x["faceId"]
         faceAttributes= x["faceAttributes"]
         age= faceAttributes["age"]
         suma_age=suma_age+ age
         cont=cont+1
         emotion=faceAttributes["emotion"]
         happiness= emotion["happiness"]
         lista2=[]
         lista2.append(faceId)
         lista2.append(happiness)
         lista.append(lista2)


        prom=prom+suma_age//cont 
        y=higher(lista)

        if y [1]>prom:
         str_out= "the face with the percentage of happiness higher and greater than the average age is:  ", y[0], " with a average of: ", y[1]
         print("the face with the percentage of happiness higher and greater than the average age is:  ", y[0], " with a average of: ", y[1])
         str_out_history= ("Option 'm' was executed and the result is: ", str_out)
         history_list.append(str_out_history)
        
        else:
         str_out= "the highest happiness percentage is below the average age of the people in the photo."
         print("the highest happiness percentage is below the average age of the people in the photo.")
         str_out_history= ("Option 'm' was executed and the result is: ", str_out)
         history_list.append(str_out_history)

   La siguiente funcion muestra un rectángulo las caras de las personas con más barba patillas y bigote.
    
   
    
    def n(analysis,picture):
     '''Arguments: 
     analysis {list} -- es una lista con la información de las personas
     picture {string} -- es la imagen''' 
   
        print('\nConsultation n\n')
        facials_hairs = []
        face_rectagles = []
        for x in analysis:
         faceAttributes = x["faceAttributes"]
         face_rectagles.append(x["faceRectangle"])
         facialHair = faceAttributes["facialHair"]
         facials_hairs.append(facialHair)
        facials_hairs_aux = []

        indice = 0
        for x in facials_hairs:
         aux = x
         moustache = x['moustache']
         beard = x['beard']
         sideburns = x['sideburns']
         if moustache == 0 or beard == 0 or sideburns == 0:
            face_rectagles.pop(indice)
         else:
            indice = indice + 1

         show_image(picture, face_rectagles)
    
         str_out_history = ('Option n was executed and the result was to show the image marking the person with the most beard, sideburns and mustache')
         history_list.append(str_out_history)

   La siguiente funcion muestra a las mujeres que llevan gafas 
  
    def o(analysis):
     '''Arguments:
     analysis {list} -- es una lista con la información de las personas'''
    
       print(" \nConsultation 'o'\n ")
       cont=0

       for x in analysis:
         faceId= x["faceId"]
         faceAttributes= x["faceAttributes"]
         gender= faceAttributes["gender"]
         if gender=="female":
           glasses= faceAttributes["glasses"]
           if glasses=="ReadingGlasses":
               str_out= "the person with the faceId: ",faceId," uses glasses"
               print("the person with the faceId: ",faceId," uses glasses")
               str_out_history= ("Option 'o' was executed and the result is: ", str_out)
               history_list.append(str_out_history)
               cont=cont+1
       if cont==0:
        str_out= "none women uses glasses"
        print("none women uses glasses")
        str_out_history= ("Option 'o' was executed and the result is: ", str_out)
        history_list.append(str_out_history)

   La siguiente funcion muestra a la persona que tiene el pelo más rubio y es hombre  
   
   Argumentos: analysis {list} -- es una lista con la información de las personas 

    def p(analysis):
    '''Arguments: 
    analysis {list} -- es una lista con la información de las personas''' 
  
        print("\n Consultation 'p' \n")
        lista=[]
        for x in analysis:
         faceId= x["faceId"]
         faceAttributes= x["faceAttributes"]
         hair= faceAttributes["hair"]
         hairColor= hair["hairColor"]
         gender= faceAttributes["gender"]
         if gender=="male":
            
            for x in hairColor:
                color= x["color"]
                confidence= x["confidence"]
                if color=="blond":
                    lista3=[]
                    lista3.append(faceId)
                    lista3.append(color)
                    lista3.append(confidence)
                    lista.append(lista3)
       x= higher(lista)
       str_out= "the face ", x[0], "has the blondest hair"
       print("the face ", x[0], "has the blondest hair")
       str_out_history= ("Option 'p' was executed and the result is: ", str_out)
       history_list.append(str_out_history)    

   La siguiente funcion permite realizar dos funciones más
   
   
    
    def q():
        print("\n Consulta 'q'\n")
   
        q1(analysis)
        q2(analysis)

   La siguiente funcion muestra el más joven de todas las edades contenidas en la lista
    
    def q1(analysis):
     '''Arguments: analysis {list} -- es una lista con la información de las personas'''
    
        print('\nProposal 1\n')
        aux = 150
        ages_person = []
        predominant_emotion = []
        for x in analysis:
         faceAttributes = x["faceAttributes"]
         age_person = faceAttributes["age"]
         ages_person.append(age_person)
        print(ages_person)
        for x in ages_person: 
         if x < aux:
            aux = x

        str_out = ('The youngest age on the list is: ', aux)
        print('The youngest age on the list is: ', aux)
        str_out_history = ('Option q1 was executed and the result is: ', str_out)
        history_list.append(str_out_history)
    
  La siguiente funcion muestra el porcentaje de una persona de emoción neutral.
   
    def q2(analysis):
    '''Arguments: 
    analysis {list} -- es una lista con la información de las personas''' 
    
        print('\nProposal 2\n')
        emotions_person = []
        for x in analysis:
         faceAttributes = x["faceAttributes"]
         emotions_person = faceAttributes["emotion"]
        
        str_out = ('The percentage of neutral emotion is: ', emotions_person['neutral'])
        print('The percentage of neutral emotion is: ', emotions_person['neutral'])
        str_out_history = ('Option q2 was executed and the result is: ', str_out)
        history_list.append(str_out_history)

  La siguiente funcion guarda cada consulta que se hace en una lista para mantener un historial 
   
    def r(analysis):
    ''Arguments: 
    analysis {lista} -- es una lista con la información de las personas'''
    
        print("\nhistory of consultation\n")
        for x in history_list:
         print(x)
         print('\n')

   La siguiente funcion realiza un informe de consulta con 5 imágenes que muestran más de 5 
   personas por imagen e incluye todas las consultas descritas anteriormente.

    def s():
  
        cont=0
        z=1
        print("\n Query of 5 images\n")
        while cont<5:
         print("image #",z)
         picture = input("\nEnter the path of the image:")
         analysis2 = emotions(picture)
         total_consultation(analysis2,picture)
         cont=cont+1
         z=z+1

   La siguiente funcion permite ejecutar todas las consultas
    
    def total_consultation(analysis,picture):
    '''Arguments :

    picture {string} -- is the image
    analysis {list} -- is a list with the information of the people'''
   
        a(analysis)
        b(analysis)
        c(analysis)
        d(analysis)
        e(analysis)
        f(analysis)
        g(analysis)
        h(analysis)
        i(analysis)
        j(analysis)
        k(analysis,picture)
        l(analysis)
        m(analysis)
        n(analysis,picture)
        o(analysis)
        p(analysis)
        q()
        r(analysis)
    
  El codigo a continoacion crea un  menu de consultas
   
    if __name__ == "__main__":
         picture = input("Enter the path of the image:")
         analysis = emotions(picture)
         point3(picture)
         inquiries= input("Enter a letter of the query to be made: ")
         while inquiries != "t":
          if inquiries=="a":
            a(analysis)
          elif inquiries=="b":
            b(analysis)
          elif inquiries=="c":
            c(analysis)
          elif inquiries=="d":
            d(analysis)
          elif inquiries=="e":
            e(analysis)
          elif inquiries=="f":
            f(analysis)
          elif inquiries=="g":
            g(analysis)
          elif inquiries=="h":
            h(analysis)
          elif inquiries=="i":
            i(analysis)
          elif inquiries=="j":
            j(analysis)
          elif inquiries=="k":
            k(analysis,picture)
          elif inquiries=="l":
            l(analysis)
          elif inquiries=="m":
            m(analysis)
          elif inquiries=="n":
            n(analysis,picture)
          elif inquiries=="o":
            o(analysis)
          elif inquiries=="p":
            p(analysis)
          elif inquiries=="q":
            q()
          elif inquiries=="r":
            r(analysis)
          elif inquiries=="s":
            s()
 
          else:
            print("error")
          inquiries= input("\nenter a letter of the query to be made (Type 't' to exit): ")

   Finalmente tenemos la funcion multiproceso, lo que hace es optimizar un proceso a realizar
   
   Función Multiproceso

         print("Función Multiproceso")
         with concurrent.futures.ProcessPoolExecutor() as executor: 
          inicio = default_timer()
          a(analysis)            
          fin = default_timer()
          executor.submit(a)  
         print("El ejercicio a con la función multiproceso tardó: ", inicio-fin)
         print("\n")
  
   La funcion secuencial nos muestra el tiempo sin ser optimizado con la funcion multiproceso

   Función Secuencial
    
         print("Función Secuencial")
         inicio = default_timer()
         a(analysis)
         fin = default_timer()
        print("El ejercicio a con la función secuencial tardó: ", inicio-fin)
        print("\n")
        
Al ejecutar el código, la consola interactuará con el usuario solicitando el path (dirección) de la imagen que desea utilizar con el programa, al ser ingresado, mostrará la información de cada una de las personas identificadas en la imagen, luego solicitará qué opción desea realizar, siguiente de esto se imprimirá el resultado de la opción elegida, recibiendo también la duración de la ejecución hecha, tanto mediante con el multiprocesamiento como la secuencial.
        
