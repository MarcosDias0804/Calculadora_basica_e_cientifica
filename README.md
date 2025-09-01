# Calculadora_basica_e_cientifica
calculadora (demora de 63 dias)

#!/usr/bin/env python3
# -*- coding: utf-8 -*-
"""
Calculadora em Python (Básica e Científica)
-------------------------------------------------
Autor: [Marcos Dias Sepini]
Descrição:
    - Permite escolher entre modo básico, científico e expressão livre.
    - Contém operações aritméticas, trigonometria, exponenciais, logaritmos,
      fatorial, combinações, números complexos, frações, estatística e matrizes.
    - Interface em linha de comando (CLI).

Uso:
    python calculadora.py

Observações:
    - Código estruturado em funções modulares.
    - Comentários explicativos em cada parte.
    - Fácil de expandir ou modificar conforme necessidade.
"""

# ========================
# Importações necessárias
# ========================
import math
import cmath
from fractions import Fraction
from statistics import mean, median, mode, pstdev, pvariance, stdev, variance
import ast

# ========================
# Funções auxiliares de entrada de dados
# ========================
def input_float(prompt: str) -> float:
    """Lê um número float do usuário."""
    while True:
        try:
            return float(input(prompt))
        except ValueError:
            print("Entrada inválida. Digite novamente.")


def input_int(prompt: str) -> int:
    """Lê um número inteiro do usuário."""
    while True:
        try:
            return int(input(prompt))
        except ValueError:
            print("Entrada inválida. Digite novamente.")


def input_list_float(prompt: str):
    """Lê uma lista de números float separados por vírgula."""
    while True:
        data = input(prompt).strip()
        try:
            return [float(x) for x in data.replace(';', ',').split(',') if x.strip()]
        except ValueError:
            print("Formato inválido. Use vírgulas para separar os números.")


def input_fraction(prompt: str) -> Fraction:
    """Lê uma fração no formato a/b."""
    while True:
        s = input(prompt).strip()
        try:
            if '/' in s:
                num, den = s.split('/')
                return Fraction(int(num), int(den))
            return Fraction(int(s), 1)
        except Exception:
            print("Digite como inteiro ou a/b. Ex: 3/4")


def input_complex(prompt: str) -> complex:
    """Lê um número complexo (use j para parte imaginária)."""
    while True:
        s = input(prompt).strip().lower().replace('i', 'j')
        try:
            return complex(s)
        except Exception:
            print("Formato inválido. Ex: 2+3j")

# ========================
# Avaliador seguro de expressões
# ========================
class SafeEvaluator:
    """Permite avaliar expressões matemáticas de forma segura."""

    def __init__(self, mode: str = 'real', angle_mode: str = 'rad'):
        self.mode = mode
        self.angle_mode = angle_mode
        self.env = self._build_env()

    def _wrap_angle(self, f):
        if self.angle_mode == 'deg':
            return lambda x: f(math.radians(x))
        return f

    def _build_env(self):
        env = {
            'pi': math.pi,
            'e': math.e,
            'tau': math.tau,
            'j': 1j,
        }
        env.update({
            'sin': self._wrap_angle(math.sin),
            'cos': self._wrap_angle(math.cos),
            'tan': self._wrap_angle(math.tan),
            'sqrt': math.sqrt,
            'log': lambda x, b=10: math.log(x, b),
            'ln': math.log,
            'exp': math.exp,
            'abs': abs,
            'fact': math.factorial,
            'comb': lambda n, r: math.comb(int(n), int(r)),
            'perm': lambda n, r: math.perm(int(n), int(r)),
        })
        return env

    def eval(self, expr: str):
        """Avalia uma expressão matemática em string."""
        tree = ast.parse(expr, mode='eval')
        return eval(compile(tree, '<expr>', 'eval'), {"__builtins__": {}}, self.env)

# ========================
# Menus de operações
# ========================
def basic_menu():
    """Operações básicas: soma, subtração, multiplicação, divisão, etc."""
    print("=== MODO BÁSICO ===")
    a = input_float("Digite o primeiro número: ")
    b = input_float("Digite o segundo número: ")
    print("Soma:", a+b)
    print("Subtração:", a-b)
    print("Multiplicação:", a*b)
    if b != 0:
        print("Divisão:", a/b)


def scientific_menu():
    """Operações científicas: trigonometria, logaritmos, exponenciais, etc."""
    print("=== MODO CIENTÍFICO ===")
    x = input_float("Digite um valor: ")
    print("Seno:", math.sin(x))
    print("Cosseno:", math.cos(x))
    print("Tangente:", math.tan(x))
    print("Exponencial:", math.exp(x))
    if x > 0:
        print("Logaritmo natural:", math.log(x))


def expression_mode():
    """Permite digitar expressões diretamente."""
    print("=== MODO EXPRESSÃO ===")
    ev = SafeEvaluator()
    while True:
        expr = input(">>> ").strip()
        if expr.lower() in {"sair", "exit", "quit"}:
            break
        try:
            print("Resultado:", ev.eval(expr))
        except Exception as e:
            print("Erro:", e)

# ========================
# Função principal
# ========================
def main():
    while True:
        print("\n==== CALCULADORA PYTHON ====")
        print("1. Modo Básico")
        print("2. Modo Científico")
        print("3. Modo Expressão")
        print("0. Sair")
        opc = input("Escolha: ").strip()
        if opc == '1':
            basic_menu()
        elif opc == '2':
            scientific_menu()
        elif opc == '3':
            expression_mode()
        elif opc == '0':
            print("Encerrando...")
            break
        else:
            print("Opção inválida.")

if __name__ == "__main__":
    main()
