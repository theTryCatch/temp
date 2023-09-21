Hi Paul,

Unfortunately, I can't display the actual error reason(s) in the header due to various limitations we have with the TCM2 API. Therefore, I should show them in the description field.

Considering the age and maturity levels of this tool, I'm not yet comfortable with not displaying the actual error messages and customizing them for every exception to improve user readability. Therefore, you may still encounter lengthier exceptions. In such cases, please let us know so that I can handle those exceptions as I did today.

I've attempted to remove all unnecessary information from the actual error if I can find one or more reasons in the error message. This way, the end user can read the message more comfortably. If I can't find exception reasons, then I'm displaying the actual error message intact, which leaves traces for me to investigate further.

Below is a sample error when an invalid turnover executor is provided.