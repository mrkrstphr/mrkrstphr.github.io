---
layout: post
title: ! 'Zend Framework Sucks: One Validate Callback per Form Field'
excerpt: One Validate Callback per Form Field is only one of the reasons why Zend_Form is a giant piece of crap.
categories:
- Programming
tags:
- fail
- PHP
- Zend Framework
status: publish
type: post
published: true
---

I completely loathe `Zend_Form` and it's silly validation system, but I'll save that for another time and just
concentrate on a specific example: You can only have one validation callback per form element.

There's the awesome `Zend_Validate_Callback` which allows us to try to make up for other validation shortcomings by
allowing us to define our own validation rules.

But apparently you can only add one callback validator per form element.

### Why is this an issue?

`Zend_Validate_Callback` provides silly error messages (`"% is not valid"`). So it's desirable to override this
message, but if you're doing multiple custom validations, you'll want multiple messages depending on how validation
failed. Well, you can't add multiple messages, so the intuitive (as close as you can get to intuitive with ZF) thing
to do would be to break it out into multiple validators and add a helpful message for each. You can't do that!

Thanks, ZF.

A possible solution would be to pass an instance of the object to the callback via `setOptions()`. But the messages
are private, with no method to change them.

### Hackish solution: create our own validator.

```php
<?php

require_once 'Zend/Validate/Abstract.php';
require_once 'Zend/Validate/Callback.php';

class My_Validate_Callback extends Zend_Validate_Callback {
  public function setMessage($message, $value) {
    if(isset($this->_messageTemplates[$message]))
      $this->_messageTemplates[$message] = $value;
  }

  public function isValid($value) {
    $this->_setValue($value);

    $options  = $this->getOptions();
    $callback = $this->getCallback();
    $args     = func_get_args();
    $options  = array_merge($args, $options);

    $options[] = &$this;

    try {
      if (!call_user_func_array($callback, $options)) {
        $this->_error(self::INVALID_VALUE);
        return false;
      }
    } catch (Exception $e) {
      $this->_error(self::INVALID_CALLBACK);
      return false;
    }

    return true;
  }
}
```

All we're doing here is extending `Zend_Validate_Callback` and (1) adding a method to override the message templates
and (2) passing a reference to this object to the callback method.

Complex. Insane. Should be unneeded. Zend Framework.
