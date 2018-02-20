# Working with events in Arachne/Forms

## Example of PRE_SUBMIT event usage
Example of form with PRE_SUBMIT event listener:
```
class ContainerCreateForm extends AbstractType
{
    public function buildForm(FormBuilderInterface $builder, array $options)
    {
        $builder
          ->add('inputType', ChoiceType::class, array(
              'choices' => array(
                  'Text input' => 'text',
                  'Upload input' => 'upload'
              ),
              'placeholder' => 'Choose input type please',
              'attr' => [
                  'class' => 'input-select'
              ]
          ));
            
        $builder->addEventListener(FormEvents::PRE_SUBMIT,
          function (FormEvent $event) {
            $data = $event->getData();
            $form = $event->getForm();

            switch($data['inputType'])
            {
                case 'text':
                    $form->add('title', TextType::class);
                    break;
                case 'upload':
                    $form->add('images', FileType::class, [
                        'multiple' => true,
                        'attr' => [
                            'accept' => 'image/*',
                            'multiple' => 'multiple',
                            'class' => 'container-images'
                        ]
                    ]);
                    break;
            }
          }
        );
    }
}
```

Example of AJAX request:
```
$(document).on('change', '.input-select', function () {
    //select elements to work with
    var selectElement = $(this);
    var form = $(this).closest('form');

    // Simulate form data
    var data = {};
    
    //add selected value to data
    data[$(this).attr('name')] = $(this).val();
    
    //add nette _do signal
    data['_do'] = form.attr('name') + '-form-render';
    
    //add object which we want to redraw
    data['entityForm-form-fields'] = [form.attr('name')];
    
    // Submit data via AJAX to the form's action path.
    $.ajax({
        url : form.attr('action'),
        type: form.attr('method'),
        data : data,
        success: function(response) {
            //replace form with altered one from response
            form.replaceWith(response.widgets[form.attr('name')]);
        }
    });
});
```
