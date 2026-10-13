// ============================================
// FACTORIZACIÓN LU DE UNA MATRIZ 3x3
// MÉTODO DE DOOLITTLE
// Acepta enteros, decimales y fracciones
// ============================================

using System;

class Program
{
    // ============================================
    // ESTRUCTURA PARA MANEJAR FRACCIONES
    // ============================================
    public struct Fraction
    {
        public long Numerator { get; private set; }
        public long Denominator { get; private set; }

        public Fraction(long numerator, long denominator = 1)
        {
            if (denominator == 0)
                throw new ArgumentException("El denominador no puede ser cero.");

            long gcd = GCD(Math.Abs(numerator), Math.Abs(denominator));
            numerator /= gcd;
            denominator /= gcd;

            if (denominator < 0)
            {
                numerator = -numerator;
                denominator = -denominator;
            }

            Numerator = numerator;
            Denominator = denominator;
        }

        private static long GCD(long a, long b)
        {
            while (b != 0)
            {
                long temp = b;
                b = a % b;
                a = temp;
            }
            return a;
        }

        public static Fraction operator +(Fraction a, Fraction b)
        {
            return new Fraction(
                a.Numerator * b.Denominator + b.Numerator * a.Denominator,
                a.Denominator * b.Denominator
            );
        }

        public static Fraction operator -(Fraction a, Fraction b)
        {
            return new Fraction(
                a.Numerator * b.Denominator - b.Numerator * a.Denominator,
                a.Denominator * b.Denominator
            );
        }

        public static Fraction operator *(Fraction a, Fraction b)
        {
            return new Fraction(
                a.Numerator * b.Numerator,
                a.Denominator * b.Denominator
            );
        }

        public static Fraction operator /(Fraction a, Fraction b)
        {
            if (b.Numerator == 0)
                throw new DivideByZeroException("División por cero.");
            return new Fraction(
                a.Numerator * b.Denominator,
                a.Denominator * b.Numerator
            );
        }

        public static bool operator ==(Fraction a, Fraction b)
        {
            return a.Numerator == b.Numerator && a.Denominator == b.Denominator;
        }

        public static bool operator !=(Fraction a, Fraction b)
        {
            return !(a == b);
        }

        public override bool Equals(object obj)
        {
            return obj is Fraction other && this == other;
        }

        public override int GetHashCode()
        {
            return HashCode.Combine(Numerator, Denominator);
        }

        public override string ToString()
        {
            if (Denominator == 1)
                return Numerator.ToString();
            return $"{Numerator}/{Denominator}";
        }

        public static Fraction Parse(string texto)
        {
            texto = texto.Trim();

            if (texto.Contains("/"))
            {
                string[] partes = texto.Split('/');
                if (partes.Length != 2)
                    throw new FormatException();

                long num = long.Parse(partes[0].Trim());
                long den = long.Parse(partes[1].Trim());
                return new Fraction(num, den);
            }
            else
            {
                if (texto.Contains("."))
                {
                    string[] partes = texto.Split('.');
                    string entero = partes[0];
                    string decimalPart = partes[1];

                    long num = long.Parse(entero + decimalPart);
                    long den = (long)Math.Pow(10, decimalPart.Length);

                    if (texto.StartsWith("-") && num > 0)
                        num = -num;

                    return new Fraction(num, den);
                }
                else
                {
                    return new Fraction(long.Parse(texto));
                }
            }
        }
    }

    // ============================================
    // FUNCIÓN PARA INGRESAR LOS NÚMEROS
    // ============================================
    static Fraction IngresarNumero(string mensaje)
    {
        while (true)
        {
            Console.Write(mensaje);
            string dato = Console.ReadLine()?.Trim() ?? "";

            try
            {
                return Fraction.Parse(dato);
            }
            catch
            {
                Console.WriteLine();
                Console.WriteLine("Dato incorrecto.");
                Console.WriteLine("Ingrese un número entero, decimal o fracción.");
                Console.WriteLine("Ejemplos: 5, 2.5, 3/4, -7/3");
                Console.WriteLine();
            }
        }
    }

    // ============================================
    // FUNCIÓN PARA MOSTRAR MATRICES
    // ============================================
    static void MostrarMatriz(string nombre, Fraction[,] matriz)
    {
        Console.WriteLine();
        Console.WriteLine(nombre);

        for (int i = 0; i < 3; i++)
        {
            Console.Write("[ ");
            for (int j = 0; j < 3; j++)
            {
                Console.Write($"{matriz[i, j]} ");
            }
            Console.WriteLine("]");
        }
    }

    // ============================================
    // MULTIPLICACIÓN DE MATRICES 3x3
    // ============================================
    static Fraction[,] MultiplicarMatrices(Fraction[,] X, Fraction[,] Y)
    {
        Fraction[,] resultado = new Fraction[3, 3];

        for (int i = 0; i < 3; i++)
        {
            for (int j = 0; j < 3; j++)
            {
                Fraction suma = new Fraction(0);
                for (int k = 0; k < 3; k++)
                {
                    suma = suma + (X[i, k] * Y[k, j]);
                }
                resultado[i, j] = suma;
            }
        }
        return resultado;
    }

    // ============================================
    // COMPARAR SI DOS MATRICES SON IGUALES
    // ============================================
    static bool MatricesIguales(Fraction[,] X, Fraction[,] Y)
    {
        for (int i = 0; i < 3; i++)
        {
            for (int j = 0; j < 3; j++)
            {
                if (X[i, j] != Y[i, j])
                    return false;
            }
        }
        return true;
    }

    static void Main()
    {
        Console.WriteLine("======================================");
        Console.WriteLine("   FACTORIZACIÓN LU DE UNA MATRIZ 3x3");
        Console.WriteLine("           MÉTODO DE DOOLITTLE");
        Console.WriteLine("======================================");
        Console.WriteLine();
        Console.WriteLine("Ejemplos de datos válidos:");
        Console.WriteLine("Entero:     5");
        Console.WriteLine("Decimal:    2.5");
        Console.WriteLine("Fracción:   3/4");
        Console.WriteLine("Fracción:  -5/2");
        Console.WriteLine();

        // ============================================
        // INGRESAR MATRIZ A
        // ============================================
        Console.WriteLine("INGRESE LOS ELEMENTOS DE LA MATRIZ A");
        Console.WriteLine("--------------------------------------");

        Fraction a11 = IngresarNumero("Ingrese a11: ");
        Fraction a12 = IngresarNumero("Ingrese a12: ");
        Fraction a13 = IngresarNumero("Ingrese a13: ");

        Fraction a21 = IngresarNumero("Ingrese a21: ");
        Fraction a22 = IngresarNumero("Ingrese a22: ");
        Fraction a23 = IngresarNumero("Ingrese a23: ");

        Fraction a31 = IngresarNumero("Ingrese a31: ");
        Fraction a32 = IngresarNumero("Ingrese a32: ");
        Fraction a33 = IngresarNumero("Ingrese a33: ");

        // ============================================
        // MATRIZ A
        // ============================================
        Fraction[,] A = new Fraction[3, 3]
        {
            { a11, a12, a13 },
            { a21, a22, a23 },
            { a31, a32, a33 }
        };

        // ============================================
        // MATRIZ L
        // ============================================
        Fraction[,] L = new Fraction[3, 3]
        {
            { new Fraction(1), new Fraction(0), new Fraction(0) },
            { new Fraction(0), new Fraction(1), new Fraction(0) },
            { new Fraction(0), new Fraction(0), new Fraction(1) }
        };

        // ============================================
        // MATRIZ U
        // ============================================
        Fraction[,] U = new Fraction[3, 3]
        {
            { new Fraction(0), new Fraction(0), new Fraction(0) },
            { new Fraction(0), new Fraction(0), new Fraction(0) },
            { new Fraction(0), new Fraction(0), new Fraction(0) }
        };

        // ============================================
        // FACTORIZACIÓN LU
        // MÉTODO DE DOOLITTLE
        // ============================================

        // Primera fila de U
        U[0, 0] = A[0, 0];
        U[0, 1] = A[0, 1];
        U[0, 2] = A[0, 2];

        // Verificar primer pivote
        if (U[0, 0] == new Fraction(0))
        {
            Console.WriteLine();
            Console.WriteLine("No se puede realizar la factorización LU.");
            Console.WriteLine("El primer pivote es cero.");
        }
        else
        {
            // Primera columna de L
            L[1, 0] = A[1, 0] / U[0, 0];
            L[2, 0] = A[2, 0] / U[0, 0];

            // Segunda fila de U
            U[1, 1] = A[1, 1] - L[1, 0] * U[0, 1];
            U[1, 2] = A[1, 2] - L[1, 0] * U[0, 2];

            // Verificar segundo pivote
            if (U[1, 1] == new Fraction(0))
            {
                Console.WriteLine();
                Console.WriteLine("No se puede realizar la factorización LU.");
                Console.WriteLine("El segundo pivote es cero.");
            }
            else
            {
                // Segunda columna de L
                L[2, 1] = (A[2, 1] - L[2, 0] * U[0, 1]) / U[1, 1];

                // Tercera fila de U
                U[2, 2] = A[2, 2]
                          - L[2, 0] * U[0, 2]
                          - L[2, 1] * U[1, 2];

                // ====================================
                // MOSTRAR RESULTADOS
                // ====================================
                MostrarMatriz("MATRIZ A:", A);
                MostrarMatriz("MATRIZ L:", L);
                MostrarMatriz("MATRIZ U:", U);

                // ====================================
                // VERIFICACIÓN: L × U
                // ====================================
                Fraction[,] producto = MultiplicarMatrices(L, U);

                MostrarMatriz("PRODUCTO L × U:", producto);

                Console.WriteLine();
                Console.WriteLine("======================================");
                Console.WriteLine("VERIFICACIÓN:");

                if (MatricesIguales(A, producto))
                {
                    Console.WriteLine("¡CORRECTO!  L × U = A");
                }
                else
                {
                    Console.WriteLine("ERROR:  L × U  ≠  A");
                }

                Console.WriteLine("======================================");
                Console.WriteLine("FACTORIZACIÓN:");
                Console.WriteLine("A = L × U");
                Console.WriteLine("======================================");
            }
        }

        Console.WriteLine();
        Console.WriteLine("Presione cualquier tecla para salir...");
        Console.ReadKey();
    }
}