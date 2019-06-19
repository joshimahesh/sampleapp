const commander = require("commander");
var init = require('./cli/flow');
commander
  .version("0.0.1", "-v, --version")
  .command("new <name>")
  .alias("n")
  .option(
    "-f, --framework [framework]",
    "Test Runner Framework",
    /^(mocha|jasmine)/g,
    "jasmin"
  )
  .action((name, options) => {
    console.log("-->" + name);
    console.log(options.framework);
    
    //get(name,options.framework);
  });

  commander
  .command('generate <test>')
  .alias('g')
  .option('-e, --ext [ext]', "postfix append string", 'spec')
  .action((test, options) => { 
      
          let projectdir = process.cwd();
          let extension = options.ext;
         // let testCase = new TestCase(projectdir, test, extension);
          console.log(projectdir)
          console.log(test)
          console.log(extension)
          //testCase.createTestFiles();
      
  });
commander.parse(process.argv);
//console.log();

if (!commander.args.length) {
  commander.help();
}
function get(name, framework) {
    console.log("Frp,inti");
    console.log(name);
    console.log(framework);
  }

  
