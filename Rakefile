require 'erb'

# ------------------------------------------------------------------------------------------------------------------------
#  Generated by jasmine
# ------------------------------------------------------------------------------------------------------------------------

begin
  require 'jasmine'
  load 'jasmine/tasks/jasmine.rake'
rescue LoadError
  task :jasmine do
    abort "Jasmine is not available. In order to run jasmine, you must: (sudo) gem install jasmine"
  end
end

# ------------------------------------------------------------------------------------------------------------------------
#  Helper methods
# ------------------------------------------------------------------------------------------------------------------------

# Generate the epub cfi library
def render_cfi_library_template(template_file_path, output_file_path, path_from_executing_dir)

    # Read each of the library components
    cfi_parser = File.read(path_from_executing_dir + '/src/models/epubcfi.js')
    cfi_interpreter = File.read(path_from_executing_dir + '/src/models/cfi_instructions.js')
    cfi_instructions = File.read(path_from_executing_dir + '/src/models/cfi_interpreter.js')
    cfi_generator = File.read(path_from_executing_dir + '/src/models/cfi_generator.js')
    runtime_errors = File.read(path_from_executing_dir + '/src/models/runtime_errors.js')

    template = File.read(template_file_path)
    erb = ERB.new(template)
    
    # Generate library
    File.open(output_file_path, "w") do |f|
        f.puts erb.result(binding)
    end
end

def get_template_name
    return "cfi_library_template.js.erb"
end

def get_test_template_name
    return "cfi_library_test_template.js.erb"
end

def get_module_name
    return "epub_cfi.js"
end

def get_this_directory
    return File.dirname(__FILE__)
end

def get_parent_directory
    return File.expand_path("..", File.dirname(__FILE__))
end

# ------------------------------------------------------------------------------------------------------------------------
#  Tasks
# ------------------------------------------------------------------------------------------------------------------------

# Generate the epub cfi parser
task :gen_parser do
    puts `pegjs -e EPUBcfi.Parser cfi_grammar/epubcfi.pegjs`
    mv 'cfi_grammar/epubcfi.js', 'src/models/epubcfi.js'
end 

task :test_parser => [:gen_parser, :jasmine]

# Rationale: Tasks in this Rakefile can be executed from some other directory using the following command: 
#   rake -f path/to/this/Rakefile gen_module. If this task is executed in this manner, the current working directory
#   is not relative to this Rakefile and thus the paths must be correctly resolved with the passed parameters.
desc "get the dependencies for the epub cfi module"
task :copy_dependencies do
  
  module_name = get_module_name()
  puts ":copy_dependencies => #{module_name}"
  
    path_to_this_dir = get_this_directory()
    parent_dir = get_parent_directory()

    #Rake::Task[:gen_module].invoke()
    
    # Import dependencies
    lib_dependency_filenames = [
        "jquery-1.9.1.js"
    ]

    module_dependency_filenames = []

    # Get all the lib dependencies
    lib_dependency_filenames.each do |filename|
      puts "---> #{filename}"
        `cp -a "#{parent_dir}/lib/#{filename}" "#{path_to_this_dir}/lib/#{filename}"`
    end

    # Get all the epub module dependencies
    module_dependency_filenames.each do |filename|
      puts "---> #{filename}"
        `cp -a "#{parent_dir}/bin/#{filename}" "#{path_to_this_dir}/lib/#{filename}"`
    end
end

desc "render the epub cfi module erb template"
task :gen_module do
  
  module_name = get_module_name()
  puts ":gen_module => #{module_name}"
  
    path_to_this_dir = get_this_directory()
    parent_dir = get_parent_directory()
    template_file_path = path_to_this_dir + "/src/templates/" + get_template_name()
    template_test_file_path = path_to_this_dir + "/src/templates/" + get_test_template_name()
    output_module_file_path = path_to_this_dir + "/bin/#{module_name}"
    output_test_module_file_path = path_to_this_dir + "/spec/javascripts/vendor/#{module_name}"

    render_cfi_library_template(template_file_path, output_module_file_path, path_to_this_dir)
    render_cfi_library_template(template_test_file_path, output_test_module_file_path, path_to_this_dir)
end
