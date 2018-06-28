# treeFlow

//initial data setup
var tasks=['direct based on data','array data','object data','string data','print data','look for data','process data','modify data'];
var taskArr=[];
var analyzedTasks=[];
for(var i in tasks){
    taskArr.push({id:'task'+i,desc:tasks[i]});
}
var tree={id:'start'};


//on task set in dropdown change or next button
function updateTaskTree(task,connectingTask){
    var taskIndex=tasks.indexOf(task);
    if(typeof connectingTask!=='object'){
        var connectingTaskIndex=tasks.indexOf(connectingTask);
        if(taskIndex!==-1){
            taskArr[taskIndex].connection=taskArr[connectingTaskIndex];
        }else{
            tree.connection=taskArr[connectingTaskIndex];
        }
    }else{
        for(var i in connectingTask){
            var connectingTaskIndex=tasks.indexOf(connectingTask[i]);
            if(taskIndex!==-1){
                taskArr[taskIndex].connection=taskArr[taskIndex].connection||[];
                taskArr[taskIndex].connection.push(taskArr[connectingTaskIndex]);
            }
        }
    }
}


function deleteTaskFromParentNode(obj,taskIndex){
    if(typeof obj!=='object'|| analyzedTasks.indexOf(obj.id)>-1)
        return;
    
    analyzedTasks.push(obj.id);
    if(!Array.isArray(obj['connection'])){

        if(obj['connection'] && obj['connection'].id==='task'+taskIndex){
            delete obj.connection;
        }else{
            deleteTaskFromParentNode(obj['connection'],taskIndex)
        } 
    }else{
        var indexToBeRemoved=-1;
        for(var i in obj['connection']){
            if(obj['connection'][i] && obj['connection'][i].id==='task'+taskIndex){
                indexToBeRemoved=i;
            }else{
                deleteTaskFromParentNode(obj['connection'][i],taskIndex)
            }
        }
        indexToBeRemoved>-1 && obj.connection.splice(indexToBeRemoved,1);
    }
    
}


function deleteTask(task){
    analyzedTasks=[];
    var taskIndex=tasks.indexOf(task);
    deleteTaskFromParentNode(tree,taskIndex);
}

function getListOfNodesNotRequired(){
   for(var i in taskArr){
      if(analyzedTasks.indexOf(taskArr[i].id)==-1){
          taskArr[i]={id:'task'+i,desc:tasks[i]};
          console.log(tasks[i]);
      }
   }
}

/*********************calls from ui based on actions*************************************/

//first task setting
updateTaskTree('start','look for data');
//second task setting
updateTaskTree('look for data','process data');
//third task setting
updateTaskTree('process data','modify data');
//fourth task setting
updateTaskTree('modify data','direct based on data');
//fifth task - conditional
updateTaskTree('direct based on data',['array data','object data','string data']);

updateTaskTree('array data','process data');
updateTaskTree('object data','print data');
updateTaskTree('string data','print data');

//deleteTasks on modification
//deleteTask('array data');
//deleteTask('string data');
//getListOfNodesNotRequired();
