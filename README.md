# Elaboración de una Api con Ruby on Rails version 6.1.4
1. Ejecutar en la consola:  
rails new (nombre del proyecto) --api

2. Luego ejecutar scaffold:   
rails g scaffold user 

scaffold: Es un generador de código el cual nos permite tener las funcionalidades básicas de administración de un modelo, es decir el CRUD (Create, Read, Update, Delete)

3. Luego ir al archivo generado en migrate:
Database Migrations/migrate/xxxxxx_create_users.rb

Dentro del archivo estructuramos la creación de la tabla quedando así:

    class CreateUsers < ActiveRecord::Migration[6.1]
      def change
        create_table :users do |t|
          t.string :name
          t.string :email
          t.string :phone
          t.boolean :active, default: true
          t.timestamps
        end
      end
    end


4. Configuramos la base de datos
si vamos a utilizar una base de datos (postgres o mysql, etc...) ir a

Configuration/database.yml y configuramos:

    development:
       <<: *default
       database: Nombre de la base de datos
       username: postgres
       password: postgres
       host: 127.0.0.1
       port: 5432    
      
      
Y si no utilizamos ninguna base de datos por defecto utilizaremos sqlite3.

4. Ejecutamos el siguiente comando:
rails db:migrate

5. Después de migrate ya tenemos la base de datos creada, para las pruebas ingresar a la base de datos y crear uno o dos registros.

6. Ir a Routes y configurar:

       Rails.application.routes.draw do
           namespace :api do
            namespace :v1 do
             resources :users
            end
           end
       end
       
 7. Ir Controllers/api/v1/users_controller.rb y quedaría:

        module Api::V1
          class UsersController < ApplicationController
            before_action :set_user, only: [:show, :update, :destroy]

            # GET /users
            def index
              @users = User.all

              render json: @users
            end

            # GET /users/1
            def show
              render json: @user
            end

            # POST /users
            def create
              @user = User.new(user_params)

              if @user.save
                render json: @user
              else
                render json: @user.errors, status: :unprocessable_entity
              end
            end

            # PATCH/PUT /users/1
            def update
              if @user.update(user_params)
                render json: @user
              else
                render json: @user.errors, status: :unprocessable_entity
              end
            end

            # DELETE /users/1
            def destroy
              @user.destroy
            end

            private
              # Use callbacks to share common setup or constraints between actions.
              def set_user
                @user = User.find(params[:id])
              end

              # Only allow a list of trusted parameters through.
              def user_params
                params.require(:user).permit(:name, :email, :phone, :active)
              end
          end
        end


8. Luego ponemos en marcha el servidor:
Rails s

9. Y en el navegador se coloca la dirección:
http://127.0.0.1:3000/api/v1/users/

Y se vería la información en formato json listo para compartirlo con el frontend que se vaya a utilizar:
	
          0:	
            id	2
            name	"pepe"
            email	"pepe@wfwe.com"
            phone	"04162345467"
            active	true
            created_at	"2021-07-03T15:30:32.762Z"
            updated_at	"2021-07-03T15:30:32.762Z"
          1:
            id	1
            name	"omer"
            email	"omer@gmail.com"
            phone	"04123471847"
            active	true
            created_at	"2021-04-22T19:44:46.038Z"
            updated_at	"2021-04-22T19:44:46.038Z"

10. Las aplicaciones frontend (Node + ReactJS) y backend (Rails API) que se ejecutan en diferentes puertos experimentarán problemas de comunicación a menos que CORS esté configurado.

CORS define un mecanismo de acceso entre dominios que permite a AJAX lograr el acceso entre dominios.

10.1. Añadir rack-cors gem a Gemfile y bundle install.

10.2. Luego abrir el archivo config/initializers/cors.rb.
Tenemos que ver el siguiente codigo, (sino está, ponerlo o descomentarlo):

	Rails.application.config.middleware.insert_before 0, Rack::Cors do
	   allow do
	     origins '*'
	     resource '*',
	       headers: :any,
	       methods: [:get, :post, :put, :patch, :delete, :options, :head]
	   end
	 end

