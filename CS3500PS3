/// <summary> 
/// Author:    [Kyo Song] 
/// Partner:   [None] 
/// Date:      [October 5th] 
/// Course:    CS 3500, University of Utah, School of Computing 
/// Copyright: CS 3500 and [Kyo Song] - This work may not be copied for use in Academic Coursework. 
/// 
/// I, [Kyo Song], certify that I wrote this code from scratch and did not copy it in part or whole from  
/// another source.  All references used in the completion of the assignment are cited in my README file. 
/// 
/// File Contents 
/// 
///    [The file Formula.cs contains formula constructors with delegates passed paramer to examine whether inputed formula is in valid syntax.
///    It contains operator overloading methods to update operators and methods for equality to compare two objects in convenience.
///    Also, exception classes for formula are present.] 
/// </summary>

using System;
using System.Collections.Generic;
using System.Linq;
using System.Linq.Expressions;
using System.Reflection.Metadata.Ecma335;
using System.Text;
using System.Text.RegularExpressions;
using static System.Runtime.InteropServices.JavaScript.JSType;

namespace SpreadsheetUtilities
{
    /// <summary>
    /// Represents formulas written in standard infix notation using standard precedence
    /// rules.  The allowed symbols are non-negative numbers written using double-precision 
    /// floating-point syntax (without unary preceeding '-' or '+'); 
    /// variables that consist of a letter or underscore followed by 
    /// zero or more letters, underscores, or digits; parentheses; and the four operator 
    /// symbols +, -, *, and /.  
    /// 
    /// Spaces are significant only insofar that they delimit tokens.  For example, "xy" is
    /// a single variable, "x y" consists of two variables "x" and y; "x23" is a single variable; 
    /// and "x 23" consists of a variable "x" and a number "23".
    /// 
    /// Associated with every formula are two delegates:  a normalizer and a validator.  The
    /// normalizer is used to convert variables into a canonical form, and the validator is used
    /// to add extra restrictions on the validity of a variable (beyond the standard requirement 
    /// that it consist of a let ter or underscore followed by zero or more letters, underscores,
    /// or digits.)  Their use is described in detail in the constructor and method comments.
    /// </summary>
    public class Formula
    {
        private IEnumerable<string> tokenFormula = new List<string>();
        public static string Normalize(string token)
        {
            return token.ToUpper();
        }
        /// <summary>
        /// Helper method to check whether a token in tokenized formula is variable.
        /// </summary>
        public static bool IsVariable(string token)
        {
            if (token.Length == 2 && char.IsLetter(token[0]) && char.IsDigit(token[1]))
            {
                return true;
            }
            else if (token.Length > 2 && char.IsLetter(token[0]))
            {
                for (int i = 1; i < token.Length; i++)
                {
                    if (!char.IsDigit(token[i]))
                    {
                        return false;
                    }
                }
                return true;
            }
            else
            {
                if (token[0] == '_' && char.IsLetter(token[1]))
                {
                    if(token.Length == 2)
                    {
                        return true;
                    }
                    else if (token.Length > 2)
                    {
                        for (int i = 2; i < token.Length; i++)
                        {
                            if (!char.IsDigit(token[i]))
                            {
                                return false;
                            }
                        }
                        return true;
                    }
                }
            }
            return false;
        }
        public Func<string, string> NormalizeDel = new Func<string, string>(Normalize);
        public Func<string, bool> IsValidDel = new Func<string, bool>(IsVariable);


        /// <summary>
        /// Creates a Formula from a string that consists of an infix expression written as
        /// described in the class comment.  If the expression is syntactically invalid,
        /// throws a FormulaFormatException with an explanatory Message.
        /// 
        /// The associated normalizer is the identity function, and the associated validator
        /// maps every string to true.  
        /// </summary>
        public Formula(string formula) :
            this(formula, s => s, s => true)
        { 
            tokenFormula = GetTokens(formula);
        }

        /// <summary>
        /// Creates a Formula from a string that consists of an infix expression written as
        /// described in the class comment.  If the expression is syntactically incorrect,
        /// throws a FormulaFormatException with an explanatory Message.
        /// 
        /// The associated normalizer and validator are the second and third parameters,
        /// respectively.  
        /// 
        /// If the formula contains a variable v such that normalize(v) is not a legal variable, 
        /// throws a FormulaFormatException with an explanatory message. 
        /// 
        /// If the formula contains a variable v such that isValid(normalize(v)) is false,
        /// throws a FormulaFormatException with an explanatory message.
        /// 
        /// Suppose that N is a method that converts all the letters in a string to upper case, and
        /// that V is a method that returns true only if a string consists of one letter followed
        /// by one digit.  Then:
        /// 
        /// new Formula("x2+y3", N, V) should succeed
        /// new Formula("x+y3", N, V) should throw an exception, since V(N("x")) is false
        /// new Formula("2x+y3", N, V) should throw an exception, since "2x+y3" is syntactically incorrect.
        /// </summary>
        public Formula(string formula, Func<string, string> normalize, Func<string, bool> isValid)
        {
            if (string.IsNullOrEmpty(formula))
            {
                throw new FormulaFormatException("Empty or null formula.");
            }
            tokenFormula = GetTokens(formula);
            
            List<string> normalizedTok = new List<string>();

            HashSet<string> validOperators = new HashSet<string> { "+", "-", "*", "/", "(", ")" };

            //Starting Token Rule
            if (!double.TryParse(tokenFormula.First(), out double num) && !IsVariable(tokenFormula.First()) && tokenFormula.First() != "(" && !isValid(tokenFormula.First()))
            {
                throw new FormulaFormatException("Violated starting token rule. First token must be a number, a variable, or an opening parenthesis.");
            }
            //Ending Token Rule
            if (!double.TryParse(tokenFormula.Last(), out double numb) && !IsVariable(tokenFormula.Last()) && tokenFormula.Last() != ")" && !isValid(tokenFormula.Last()))
            {
                throw new FormulaFormatException("Violated ending token rule. Last token must be a number, a variable, or a closing parenthesis.");
            }


            string before = "";
            int openP = 0;
            int closeP = 0;
            int check = -1;

            foreach (string token in tokenFormula)
            {
                if (isValid(token) == true)
                {
                    check++;
                }
                

                if (!double.TryParse(token, out double n) && !validOperators.Contains(token) && !IsVariable(token) && !isValid(token))
                {
                    throw new FormulaFormatException("Invalid token.");
                }
                if (before == "")
                {
                    normalizedTok.Add(normalize(token));
                    before = token;
                    if (token == "(")
                    {
                        openP++;
                    }
                    continue;
                }

                //Extra Following Rule 
                else if (double.TryParse(before, out double number) || IsVariable(normalize(before)) || before == ")" || isValid(before))
                {
                    normalizedTok.Add(normalize(token));
                    if (!validOperators.Contains(token) && token != ")")
                    {
                        throw new FormulaFormatException("Violated Extra Following Rule. Any token that immediately follows a number, a variable, or a closing parenthesis must be either an operator or a closing parenthesis.");
                    }
                    if (token == ")")
                    {
                        closeP++;
                    }
                }
                //Parenthesis/Operator Following Rule 
                else if (validOperators.Contains(before) && before != ")")
                {
                    normalizedTok.Add(normalize(token));
                    if (!double.TryParse(token, out double numberr) && !IsVariable(normalize(token)) && token != "(" && !isValid(token))
                    {
                        throw new FormulaFormatException("Violated Parenthesis/Operator Following Rule. Any token that immediately follows an opening parenthesis or an operator must be either a number, a variable, or an opening parenthesis.");
                    }
                    if(token == "(")
                    {
                        openP++;
                    }
                }
                
                if (openP < closeP)
                {
                    throw new FormulaFormatException("Violated Right Parentheses Rule. Possible cause: lesser number of opening parentheses than closing.");
                }

                before = token;
            }

            if (check < 0)
            {
                throw new FormulaFormatException("False set for inValid delegate.");
            }

            if (openP != closeP)
            {
                throw new FormulaFormatException("Violated Balanced Parentheses Rule. Possible cause: mismatching parentheses.");
            }

            tokenFormula = normalizedTok;
        }

        /// <summary>
        /// Evaluates this Formula, using the lookup delegate to determine the values of
        /// variables.  When a variable symbol v needs to be determined, it should be looked up
        /// via lookup(normalize(v)). (Here, normalize is the normalizer that was passed to 
        /// the constructor.)
        /// 
        /// For example, if L("x") is 2, L("X") is 4, and N is a method that converts all the letters 
        /// in a string to upper case:
        /// 
        /// new Formula("x+7", N, s => true).Evaluate(L) is 11
        /// new Formula("x+7").Evaluate(L) is 9
        /// 
        /// Given a variable symbol as its parameter, lookup returns the variable's value 
        /// (if it has one) or throws an ArgumentException (otherwise).
        /// 
        /// If no undefined variables or divisions by zero are encountered when evaluating 
        /// this Formula, the value is returned.  Otherwise, a FormulaError is returned.  
        /// The Reason property of the FormulaError should have a meaningful explanation.
        ///
        /// This method should never throw an exception.
        /// </summary>
        public object Evaluate(Func<string, double> lookup)
        {
            Stack<double> valStack = new Stack<double>();
            Stack<char> opStack = new Stack<char>();

            foreach (string t in tokenFormula)
            {
                if (double.TryParse(t, out double number))
                {
                    if (opStack.Count > 0 && (opStack.Peek() == '*' || opStack.Peek() == '/'))
                    {
                        char op = opStack.Pop();
                        double val = valStack.Pop();
                        if (op == '/' && number == 0.0)
                        {
                            return new FormulaError("Divided by zero.");
                        }
                        valStack.Push(ApplyOperator(val, number, op));
                    }
                    else
                    {
                        valStack.Push(number);
                    }
                }
                else if (t == "+" || t == "-")
                {
                    while (opStack.Count > 0 && (opStack.Peek() == '+' || opStack.Peek() == '-'))
                    {
                        PopCalculatePush(valStack, opStack);
                    }
                    opStack.Push(Convert.ToChar(t));
                }
                else if (t == "*" || t == "/" || t == "(")
                {
                    opStack.Push(Convert.ToChar(t));
                }
                else if (t == ")")
                {
                    while (opStack.Peek() != '(')
                    {
                        PopCalculatePush(valStack, opStack);
                    }
                    opStack.Pop(); 

                    if (opStack.Count > 0 && (opStack.Peek() == '*' || opStack.Peek() == '/'))
                    {
                        if (opStack.Peek() == '/' && valStack.Peek() == 0)
                        {
                            return new FormulaError("Divided by zero.");
                        }
                        PopCalculatePush(valStack, opStack);
                    }
                }
                else
                {
                    double varValue;
                    try
                    {
                        varValue = lookup(t);
                    }
                    catch (Exception)
                    {
                        return new FormulaError("Cannot convert variable into a value. Possible cause: undefined variable.");
                    }

                    if (opStack.Count > 0 && (opStack.Peek() == '*' || opStack.Peek() == '/'))
                    {
                        char op = opStack.Pop();
                        double val = valStack.Pop();
                        if (op == '/' && varValue == 0)
                        {
                            return new FormulaError("Divided by zero.");
                        }
                        valStack.Push(ApplyOperator(val, varValue, op));
                    }
                    else
                    {
                        valStack.Push(varValue);
                    }
                }
            }

            while (opStack.Count > 0)
            {
                PopCalculatePush(valStack, opStack);
            }

            return valStack.Count == 1 ? valStack.Pop() : new FormulaError("Invalid expression.");
        }



        /// <summary>
        /// Enumerates the normalized versions of all of the variables that occur in this 
        /// formula.  No normalization may appear more than once in the enumeration, even 
        /// if it appears more than once in this Formula.
        /// 
        /// For example, if N is a method that converts all the letters in a string to upper case:
        /// 
        /// new Formula("x+y*z", N, s => true).GetVariables() should enumerate "X", "Y", and "Z"
        /// new Formula("x+X*z", N, s => true).GetVariables() should enumerate "X" and "Z".
        /// new Formula("x+X*z").GetVariables() should enumerate "x", "X", and "z".
        /// </summary>
        public IEnumerable<string> GetVariables()
        {
            HashSet<string> variables = new HashSet<string>();

            foreach (string token in tokenFormula)
            {
                if (IsVariable(token)) 
                {
                    variables.Add(token);
                }
            }
            return variables;
        }

        /// <summary>
        /// Returns a string containing no spaces which, if passed to the Formula
        /// constructor, will produce a Formula f such that this.Equals(f).  All of the
        /// variables in the string should be normalized.
        /// 
        /// For example, if N is a method that converts all the letters in a string to upper case:
        /// 
        /// new Formula("x + y", N, s => true).ToString() should return "X+Y"
        /// new Formula("x + Y").ToString() should return "x+Y"
        /// </summary>
        public override string ToString()
        {
            string strFormula = "";

            foreach(string token in tokenFormula)
            {
                if (double.TryParse(token, out double number))
                {
                    strFormula += number;
                }
                else
                {
                    strFormula += token;
                }
            }
            
            return strFormula;
        }

        /// <summary>
        ///  <change> make object nullable </change>
        ///
        /// If obj is null or obj is not a Formula, returns false.  Otherwise, reports
        /// whether or not this Formula and obj are equal.
        /// 
        /// Two Formulae are considered equal if they consist of the same tokens in the
        /// same order.  To determine token equality, all tokens are compared as strings 
        /// except for numeric tokens and variable tokens.
        /// Numeric tokens are considered equal if they are equal after being "normalized" 
        /// by C#'s standard conversion from string to double, then back to string. This 
        /// eliminates any inconsistencies due to limited floating point precision.
        /// Variable tokens are considered equal if their normalized forms are equal, as 
        /// defined by the provided normalizer.
        /// 
        /// For example, if N is a method that converts all the letters in a string to upper case:
        /// 
        /// new Formula("x1+y2", N, s => true).Equals(new Formula("X1  +  Y2")) is true
        /// new Formula("x1+y2").Equals(new Formula("X1+Y2")) is false
        /// new Formula("x1+y2").Equals(new Formula("y2+x1")) is false
        /// new Formula("2.0 + x7").Equals(new Formula("2.000 + x7")) is true
        /// </summary>
        public override bool Equals(object? obj)
        {

            if (obj != null && obj.GetType() == typeof(Formula))
            {
                Formula inputObj = (Formula) obj;

                if (tokenFormula.Count() == inputObj.tokenFormula.Count())
                {
                    List<string> thisTokenList = tokenFormula.ToList();
                    List<string> tokenList = inputObj.tokenFormula.ToList();

                    for (int i = 0; i < tokenFormula.Count(); i++)
                    {

                        if (double.TryParse(thisTokenList[i].ToString(), out double number) && double.TryParse(tokenList[i].ToString(), out double number2))
                        {
                            if(number != number2)
                            {
                                return false;
                            }
                        }
                        else
                        {
                            if (thisTokenList[i] != tokenList[i])
                            {
                                return false;
                            }
                        }
                    }
                    return true;
                }
            }
            return false;
        }

        /// <summary>
        ///   <change> We are now using Non-Nullable objects.  Thus neither f1 nor f2 can be null!</change>
        /// Reports whether f1 == f2, using the notion of equality from the Equals method.
        /// 
        /// </summary>
        public static bool operator ==(Formula f1, Formula f2)
        {
            if(f1.Equals(f2)) 
                return true;

            return false;
        }

        /// <summary>
        ///   <change> We are now using Non-Nullable objects.  Thus neither f1 nor f2 can be null!</change>
        ///   <change> Note: != should almost always be not ==, if you get my meaning </change>
        ///   Reports whether f1 != f2, using the notion of equality from the Equals method.
        /// </summary>
        public static bool operator !=(Formula f1, Formula f2)
        {
            if(f1.Equals(f2)) 
                return false; 

            return true;
        }

        /// <summary>
        /// Returns a hash code for this Formula.  If f1.Equals(f2), then it must be the
        /// case that f1.GetHashCode() == f2.GetHashCode().  Ideally, the probability that two 
        /// randomly-generated unequal Formulae have the same hash code should be extremely small.
        /// </summary>
        public override int GetHashCode()
        {
            return ToString().GetHashCode();
        }

        /// <summary>
        /// Given an expression, enumerates the tokens that compose it.  Tokens are left paren;
        /// right paren; one of the four operator symbols; a string consisting of a letter or underscore
        /// followed by zero or more letters, digits, or underscores; a double literal; and anything that doesn't
        /// match one of those patterns.  There are no empty tokens, and no token contains white space.
        /// </summary>
        private static IEnumerable<string> GetTokens(string formula)
        {
            // Patterns for individual tokens
            string lpPattern = @"\(";
            string rpPattern = @"\)";
            string opPattern = @"[\+\-*/]";
            string varPattern = @"[a-zA-Z_](?: [a-zA-Z_]|\d)*";
            string doublePattern = @"(?: \d+\.\d* | \d*\.\d+ | \d+ ) (?: [eE][\+-]?\d+)?";
            string spacePattern = @"\s+";

            // Overall pattern
            string pattern = string.Format("({0}) | ({1}) | ({2}) | ({3}) | ({4}) | ({5})",
                                            lpPattern, rpPattern, opPattern, varPattern, doublePattern, spacePattern);

            // Enumerate matching tokens that don't consist solely of white space.
            foreach (string s in Regex.Split(formula, pattern, RegexOptions.IgnorePatternWhitespace))
            {
                if (!Regex.IsMatch(s, @"^\s*$", RegexOptions.Singleline))
                {
                    yield return s;
                }
            }

        }


        /// <summary>
        ///   The function help reducing the redundancy by performing the operation to calculate the values.
        ///   It handles the potential error of operation between less than two values.
        ///   If there is more than one value, it performs the operation using ApplyOperator.
        /// 
        /// </summary>
        /// <param name="valStack"> valStack represents the stack that holds the numbers from the substrings[]. </param>
        /// <param name="opStack"> opStack represents the stack that holds the operators from the substrings[] </param>
        /// <returns> It is void function that simply performs operation. </returns>
        private static void PopCalculatePush(Stack<double> valStack, Stack<char> opStack)
        {
            double val2 = valStack.Pop();
            double val1 = valStack.Pop();
            char op = opStack.Pop();
            valStack.Push(ApplyOperator(val1, val2, op));
        }


        /// <summary>
        ///   The function executes the operation accordingly to the operator.
        /// 
        /// </summary>
        /// <param name="val1"> val1 represents the most recent value from the valStack. </param>
        /// <param name="val2"> val2 represents the next most recent value from the valStack. </param>
        /// <param name="op"> op represents the most recent operator from the opStack. </param>
        /// <returns> It will return the result of two value's operation. However, throws exception when expression has division by zero. </returns>
        private static double ApplyOperator(double val1, double val2, char op)
        {
            return op switch
            {
                '+' => val1 + val2,
                '-' => val1 - val2,
                '*' => val1 * val2,
                '/' => val1 / val2
            };
        }
    }

    /// <summary>
    /// Used to report syntactic errors in the argument to the Formula constructor.
    /// </summary>
    public class FormulaFormatException : Exception
    {
        /// <summary>
        /// Constructs a FormulaFormatException containing the explanatory message.
        /// </summary>
        public FormulaFormatException(string message)
            : base(message)
        {
        }
    }

    /// <summary>
    /// Used as a possible return value of the Formula.Evaluate method.
    /// </summary>
    public struct FormulaError
    {
        /// <summary>
        /// Constructs a FormulaError containing the explanatory reason.
        /// </summary>
        /// <param name="reason"></param>
        public FormulaError(string reason)
            : this()
        {
            Reason = reason;
        }

        /// <summary>
        ///  The reason why this FormulaError was created.
        /// </summary>
        public string Reason { get; private set; }
    }
}


// <change>
//   If you are using Extension methods to deal with common stack operations (e.g., checking for
//   an empty stack before peeking) you will find that the Non-Nullable checking is "biting" you.
//
//   To fix this, you have to use a little special syntax like the following:
//
//       public static bool OnTop<T>(this Stack<T> stack, T element1, T element2) where T : notnull
//
//   Notice that the "where T : notnull" tells the compiler that the Stack can contain any object
//   as long as it doesn't allow nulls!
// </change>
