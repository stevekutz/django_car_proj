#### Project Setup
- if `venv` is not installed:
    - for Mac or Linux:
        - python3 -m pip install --user virtualenv  
    - for Windows:
        - py -m pip install --user virtualenv
1) Create <a href = "https://docs.python.org/3/library/venv.html"> virtual environment </a>

     ~~~ bash
     python3 -m venv car_env
     ~~~

     - if Python 3.7 needed

     ~~~ bash
     python3.7 -m venv ~/venv/py3.7
     ~~~

     - if using Windows
     ~~~ bash
     py -m venv car_env
     ~~~


2) Activate virtual environment

    - run `deactivate` within your `venv terminal` to exit the virtual environment 

     ~~~ bash
     source car_env/bin/activate
     ~~~

     - if venv created using Python 3.7

     ~~~ bash
     source ~/venv/py3.7/bin/activate
     ~~~

     - if using Windows
     ~~~ bash
     .\env\Scripts\activate
     ~~~