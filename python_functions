
#packages to install
#pm4py
#editdistance

""" 
    import statements for importing relevant libraries
""" 

#for importing logs
from pm4py.objects.log.importer.xes import factory as xes_import_factory

#for extracting list of variants
from pm4py.algo.filtering.log.variants import variants_filter

#for Retrieving statistics of log
from pm4py.statistics.traces.log import case_statistics

#python library for fast implementation of the edit distance (Levenshtein distance) 
import editdistance

#calculates the entropy of a distribution for given probability values.
from scipy.stats import entropy

import numpy as np
import itertools

"""
 firstly we will recieve log path from user and import log
"""

#retrieve log path from user
logpath = input('Log path:')


def compute_variant_variability(logpath):
    """
       python function for computing variants in log.

        Args:
        logpath (path): The path of events log to parse
        

        Returns: Number of distinct  variants in log and 
        a dataframe listing variants and thier frequencies
    """
    log = xes_import_factory.apply(logpath)
    variants_count = case_statistics.get_variant_statistics(log)
    variants_count = sorted(variants_count, key=lambda x: x['count'], reverse=True)
    variants_count = pd.DataFrame(variants_count)
    return len(variants_count), variants_count
   
    

    
def compute_edit_distance_variability(logpath):
    """
    Python function for computing edit distance between all variants in log.
    The edit distance of (a,b) is the series of edit operations (Insertion (I), 
    Deletion(D), Substitution (S), thattransforms one STRING into another. The (Levenshtein distance) is used
    Trace variants are compared as list of strings

    Args:
        logpath (path): The path of events log to parse
        

    Returns:
       Average edit distance between all variants in log
    """
    
    log = xes_import_factory.apply(logpath)
    
    logtrace = []
    for caseindx, case in enumerate(log):
        traceEvents =[]
    
        #collate list of case traces
        for eventindx, event in enumerate(case):
            traceEvents.append(event['concept:name'])
    
        #collate list of trace events 
        logtrace.append(traceEvents)
        
        
    edit_dist = []
    for a, b in itertools.combinations(logtrace, 2):
        edit_dist.append(editdistance.eval(a,b))
        
    return np.mean(edit_dist)
    
    
def compute_my_variability(logpath):
    """
    Python function for computing TRACE ENTROPY probabilities exactly the VARIANT frequencies observed in the log.
    This entropy is restricted to the simple likelihood (frequency-based) estimator, as other more complex estimators exist.
  
    Args:
        logpath (path): The path of events log to parse
        
    Returns:
       Trace entropy of all variants in the log (base10, and base2)
    """
    
    log = xes_import_factory.apply(logpath)
    variants_count = case_statistics.get_variant_statistics(log)
    variant_trace_df = pd.DataFrame(variants_count)

    #calculate probability using the frequencies of each variant
    variant_trace_df['probability'] = variant_trace_df['count'] / sum(variant_trace_df['count'])
    
    print('Entropy (base2) and  Entropy (base10)')

    return entropy(variant_trace_df['probability'],base=2), entropy(variant_trace_df['probability'],base=10)
    
   


if __name__ == '__main__':
    compute_variant_variability(logpath)
    compute_edit_distance_variability(logpath)
    compute_my_variability(logpath)
