<template>
    <div class="container">
      <div class="row mt-5">
        <div class="col-12 border-bottom mb-5" >
          <img id="logo" class="mb-4" src='../assets/logo.png' width="20%">
          <h2 id="telacad">CADASTRO</h2>
        </div>

        <div class="col-6">
          <meta charset="UTF-8" />
          <meta
            name="viewport"
            content="width=device-width, initial-scale=1.0"
          />
          <title>Cadastro Cliente</title>
          <link
            rel="stylesheet"
            href="https://stackpath.bootstrapcdn.com/bootstrap/4.5.0/css/bootstrap.min.css"
          />

          <form method="post" action="">
            
           <div class="form-group mr-1">
                <label class="col-sm-3 col-form-label">Nome ou Razão Social:  </label>          
                <input  class="form-control" id="nome_cad" required="required" type="text" v-model="nome"/> 
            </div> 

            
            <div class="form-group mr-1">
                <label class="col-sm-3 col-form-label">CPF ou CNPJ: </label>         
                <input  class="form-control" id="identificacao" required="required" type="text" v-model="cpf"/> 
            </div>
           

            <div class="form-group mr-1">
                <label class="col-sm-3 col-form-label">Projeto: </label>   
                <select v-model="projeto"  class="form-control" >
                  <option v-for="item in projetos" :value="item.projeto" :key="item.id">{{item.projeto}}</option>
                </select>    
            </div> 
            

            <div class="form-group mr-1">
                <label class="col-sm-3 col-form-label">Data de Vinculação: </label>            
                <input  class="form-control" required="required" type="date" v-model="dataVinc"/>
            </div> 

            <div class="form-group mr-1">
                <label class="col-sm-3 col-form-label" >Estado do Projeto: </label>  
                <select  class="form-control" v-model="estadoProjeto" @change="validarFinalizacao()">
                  <option value="Em Negociação">Em Negociação</option>
                  <option value="Em Desenvolvimento">Em Desenvolvimento</option>
                  <option value="Finalizado">Finalizado</option>
                </select>    
            </div> 

            <div class="form-group mr-1" v-if="imprimirFinalizacao">
                <label>Finalizado no dia: {{finalizacao}}</label>            
            </div> 
            

            <hr />
            <div v-if="ativarEdicao">
            <button type="button" class="btn btn-primary btn-lg" @click="editar()" >Editar </button>
            &nbsp;
            <button type="button" class="btn btn-danger btn-lg" @click=" ativarEdicao=!ativarEdicao;limpar()" >Cancelar </button>
            </div>

            <div v-else>
           <button type="button" class="btn btn-primary btn-lg" @click="cadastro()" >Cadastrar </button>
            &nbsp;
            <a class="btn btn-info btn-lg" href="#" @click="listar()">Listar</a>
            </div>
          </form> 

        </div>
        <div class="col-6">
        <table class="table">
             <tr>
                <th> Nome ou razão social </th>
                <th> CPF </th>
                <th> Projeto </th>
                <th> Data Vinculação </th>
                <th> Estado do Projeto </th>
                <th>  </th>
                <th>  </th>
              </tr> 
              <tr v-for="cliente in clientes" :key="cliente.nome">
                <td>{{cliente.nome}}</td>
                <td>{{cliente.cpf}}</td>
                <td>{{cliente.projeto}}</td>
                <td>{{cliente.dataVinc}}</td>
                <td>{{cliente.estadoProjeto}}</td>
                <td><button type="button" class="btn btn-danger btn-sm" @click="remover(cliente)">Remover</button></td>
                <td><button type="button" class="btn btn-primary btn-sm" @click="atualizar(cliente)">Atualizar</button></td>
              </tr>
          </table>
        </div>
      </div>
       
    </div>  
</template>

<script>
export default {
  data() {
    this.listar()
    return {
      nome: "",
      cpf: "",
      projeto: "",
      dataVinc: "",
      estadoProjeto: "",
      imprimirFinalizacao: false,
      finalizacao: "03/01/2020",
      projetos: [{projeto: 'Projeto_X', id: 1},
                 {projeto: 'Projeto_Y', id: 2},
                 {projeto: 'Projeto_Z', id: 3}],

      clientes:[],
      ativarEdicao : false
    };
  },

  methods: {
    listar(){
      if(localStorage.getItem("clientes") != null){
        this.clientes = localStorage.getItem("clientes");
        this.clientes = JSON.parse(this.clientes);
      }
    },
  
    cadastro(){
      this.clientes.push({nome: this.nome, cpf: this.cpf, projeto: this.projeto, estadoProjeto: this.estadoProjeto, dataVinc: this.dataVinc});
      var clienteJson = JSON.stringify(this.clientes);
      localStorage.setItem("clientes", clienteJson);
      alert("Salvo com Sucesso");

      this.limpar();

    },

    limpar(){
      this.nome = " "
        this.cpf = " "
        this.projeto = " "
        this.dataVinc = " "
        this.estadoProjeto = " "

    },

    atualizar(cliente){
        this.ativarEdicao = true
        this.nome = cliente.nome
        this.cpf = cliente.cpf
        this.projeto = cliente.projeto
        this.dataVinc = cliente.dataVinc
        this.estadoProjeto = cliente.estadoProjeto

    },

    editar(){
      var cliente = {nome: this.nome, cpf: this.cpf, projeto: this.projeto, estadoProjeto: this.estadoProjeto, dataVinc: this.dataVinc}

      for(var i=0; i < this.clientes.length; i++){
          if(this.clientes[i].nome == cliente.nome ){
              this.clientes[i] = cliente 
                var clienteJson = JSON.stringify(this.clientes);     
                 localStorage.setItem("clientes",clienteJson);
                  alert("Editado com Sucesso");
                  this.listar()
                  break    
          }
      }
    },

    remover(cliente){
      
      for(var i=0; i < this.clientes.length; i++){
          if(this.clientes[i].nome == cliente.nome ){
              this.clientes.splice(i,1)
                var clienteJson = JSON.stringify(this.clientes);    
                localStorage.setItem("clientes",clienteJson);
                  alert("Removido com Sucesso");
                  break
          }
      }  
    },

    validarFinalizacao(){
      if(this.estadoProjeto == 'Finalizado'){
        this.imprimirFinalizacao = true;
      }else{
        this.imprimirFinalizacao = false;
      }
    },
  }

};
</script>
<style scoped>
  #telacad{
    color: solid black;
    font-size: 40px;
    position: absolute;
	left: 50%;
	top: 50%
  }

</style>
