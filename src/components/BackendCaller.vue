<script>
import Swal from "sweetalert2";

export default {
name: "BackendCaller",
  methods: {
    saveAttachments: function (fileList, attachTo){
      return new Promise((resolveMain, rejectMain) => {
        let insertAttachmentPromiseArr = fileList.map(file => {
          return new Promise((resolve, reject) => {
            fetch(
                "https://dev-portal-eletrico.cs44.force.com/services/apexrest/daspeweb/customer-portal/new-attachment?record-id="+attachTo,
                {
                  method: "POST", headers: {"Content-Type": file.type}, body: file
                })
                .then(responseFull => {
                  responseFull.text().then(responseText => {
                    return resolve(JSON.parse(responseText))
                  })
                })
                .catch(responseError => {
                  return reject(responseError)
                })
          })
        })

        Promise.all(insertAttachmentPromiseArr).then(resultArr => {
          console.log('resultArr: ', resultArr)
          let a = 1
          let b = 1
          if (a !== b){
            return rejectMain({})//todo
          }
          return resolveMain(resultArr)
        }).catch(resultArr => {
          console.log('resultArr: ', resultArr)
        })
      })
    },
    saveOrder: function (objStoSave){
      return new Promise((resolve, reject) => {
        fetch('https://dev-portal-eletrico.cs44.force.com/services/apexrest/daspeweb/customer-portal/new-order',
        {
          method: 'POST',
          body: JSON.stringify(objStoSave),
          headers: {
            'Content-Type': 'application/json'
          }
        }).then(responseFull => {
          responseFull.text()
          .then(responseText => {
            let response = JSON.parse(responseText)
            if (responseFull.status !== 200){
              Swal.fire({icon: 'error', title: 'Oops...', text: 'Algo deu errado ao salvar o pedido!', })
              return reject(response)
            }else{
              return resolve(response)
            }
          })
        }).catch(responseError => {
          Swal.fire({icon: 'error', title: 'Oops...', text: 'Algo deu errado!', })
          console.error('erro desconhecido')
          return reject(responseError)
        })
      })
    }
  }
}
</script>
